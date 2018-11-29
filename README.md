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

Create composite score

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
### Looking at PharmCost)
hist(InfoCompositeComplete$PharmCost)
qqnorm(InfoCompositeComplete$PharmCost)
hist(log(InfoCompositeComplete$PharmCost))
```





