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

InfoCompositeComplete$PharmCost = InfoCompositeComplete$PharmCost+1
### Looking at PPTotalPaid log make sense
hist(InfoCompositeComplete$PPTotalPaid)
qqnorm(InfoCompositeComplete$PPTotalPaid)
hist(log(InfoCompositeComplete$PPTotalPaid))
qqnorm(log(InfoCompositeComplete$PPTotalPaid))

### Create the data set 
InfoCompositeCompleteOutcome = data.frame(MissedVisits = scale(log(InfoCompositeComplete$MissedVisits)), NumServices = scale(log(InfoCompositeComplete$NumServices)), PharmCost = scale(log(InfoCompositeComplete$PharmCost)), PPTotalPaid = scale(log(InfoCompositeComplete$PPTotalPaid)))
head(InfoCompositeCompleteOutcome)


Outcome = rowMeans(InfoCompositeCompleteOutcome)
describe(Outcome)


hist(Outcome)

InfoComposite = data.frame(InfoCompositeComplete, Outcome)



InfoComposite = InfoComposite[order(-Outcome),]
head(InfoComposite)
write.csv(InfoComposite, "InfoComposite.csv", row.names = FALSE)

```





