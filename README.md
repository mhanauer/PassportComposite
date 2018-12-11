---
title: "PassportComposite"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

##################
Try the composite score with all Info data set
Steps:
Get descriptives

Figure out if there are multiple people: There are multiple people so we are taking away the first instance 

Look at normality and see if transforms are necs

Check missing data: No missing data

Create composite score: Need to add one to for phram costs variable, because you cannot take the log of zero

Order by highest scores
```{r}
head(InfoMC10_PassPort_10_18)

InfoComposite = InfoMC10_PassPort_10_18[c("MissedVisits", "NumServices", "PharmCost", "PPTotalPaid", "LastCSSrvc", "AvatarID")]

## Why are there duplicates??
InfoComposite = na.omit(InfoComposite[!duplicated(InfoComposite$AvatarID),])
dim(InfoComposite)

InfoCompositeComplete = na.omit(InfoComposite)

### Looking at MissedVisits log makes sense
hist(InfoCompositeComplete$MissedVisits)
qqnorm(InfoCompositeComplete$MissedVisits)
hist(log(InfoCompositeComplete$MissedVisits))
qqnorm(log(InfoCompositeComplete$MissedVisits))
### Looking at Number of services looks better with log
hist(InfoCompositeComplete$NumServices)
qqnorm(InfoCompositeComplete$NumServices)
hist(log(InfoCompositeComplete$NumServices))
qqnorm(log(InfoCompositeComplete$NumServices))
### Clean up Phram get rid of $$
InfoCompositeComplete$PharmCost= gsub("\\D", "", InfoCompositeComplete$PharmCost)
InfoCompositeComplete$PPTotalPaid = gsub("\\D", "", InfoCompositeComplete$PPTotalPaid)
write.csv(InfoCompositeComplete, "InfoCompositeComplete.csv", row.names = FALSE)
InfoCompositeComplete = read.csv("InfoCompositeComplete.csv", header = TRUE)
### Looking at PharmCost) log makes sense
hist(InfoCompositeComplete$PharmCost)
qqnorm(InfoCompositeComplete$PharmCost)
hist(log(InfoCompositeComplete$PharmCost))

### Had to add one to everyone, because there were some cases of zero and cannot take the log of zero
InfoCompositeComplete$PharmCost = InfoCompositeComplete$PharmCost+1
### Looking at PPTotalPaid log make sense
hist(InfoCompositeComplete$PPTotalPaid)
qqnorm(InfoCompositeComplete$PPTotalPaid)
hist(log(InfoCompositeComplete$PPTotalPaid))
qqnorm(log(InfoCompositeComplete$PPTotalPaid))

### Create kept rate
InfoCompositeComplete$MissedRate = InfoCompositeComplete$MissedVisits / InfoCompositeComplete$NumServices
hist(InfoCompositeComplete$MissedRate)
qqnorm(InfoCompositeComplete$MissedRate)
hist(log(InfoCompositeComplete$MissedRate))
qqnorm(log(InfoCompositeComplete$MissedRate))

###Create cost per session rate for both
## Not very normal so maybe keep this one out
InfoCompositeComplete$CostPharm_rate = InfoCompositeComplete$NumServices / InfoCompositeComplete$PharmCost
hist(InfoCompositeComplete$CostPharm_rate)
qqnorm(InfoCompositeComplete$CostPharm_rate)
hist(log(InfoCompositeComplete$CostPharm_rate))
qqnorm(log(InfoCompositeComplete$CostPharm_rate))

InfoCompositeComplete$CostTotal_rate = InfoCompositeComplete$NumServices / InfoCompositeComplete$PPTotalPaid
hist(InfoCompositeComplete$CostTotal_rate)
qqnorm(InfoCompositeComplete$CostTotal_rate)
hist(log(InfoCompositeComplete$CostTotal_rate))
qqnorm(log(InfoCompositeComplete$CostTotal_rate))


### Create the data set 
## Just use number of services, because that makes more conceptual sense then kept rate, because a higher missed rate means less services and possibly less money spent so all the indicators are not going in the same direction.  They may be countering them.  However, it could be the case the a high missed rate means with high services means that are costing the most per service.  Maybe that is what we want.  Cost per service?  So create those variables.  But which services are associated with pharm and which are assoicated with total?  Maybe add all of them??  Maybe it is ok to offset them, because they balance each other out.  If we have someone with a high missed rate, but aren't costing that much, then maybe not as worried, if we have someone who is coming all of the time, but still cost us a lot, then we have some problem
InfoCompositeCompleteOutcome = data.frame(MissedRate = scale(log(InfoCompositeComplete$MissedRate)), PharmCost = scale(log(InfoCompositeComplete$PharmCost)),CostTotal_rate =  scale(log(InfoCompositeComplete$CostTotal_rate)), PPTotalPaid = scale(log(InfoCompositeComplete$PPTotalPaid)))
head(InfoCompositeCompleteOutcome)

## See if the variables are correlation
cor(InfoCompositeCompleteOutcome)

Outcome = rowMeans(InfoCompositeCompleteOutcome)
describe(Outcome)


hist(Outcome)

InfoComposite = data.frame(InfoCompositeComplete, Outcome)


InfoComposite = InfoComposite[order(-Outcome),]
head(InfoComposite)
write.csv(InfoComposite, "InfoComposite.csv", row.names = FALSE)

```
Other variables if you can match?
Number of visits to the ER and maybe number of visits to other expensive places like hosptial

Another problem is that missed visits is larger than number of services, so a missed rate may not make sense.




