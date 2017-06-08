---
title: "ReadingRandomization"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

Here we are going to create the data (one random and that works for each student)
```{r}
set.seed(12345)
readingData = data.frame(id = c(1:10), iq = rnorm(10), level = rnorm(10))
readingData = as.data.frame(readingData)
iq = scale(readingData$iq , center = TRUE, scale = TRUE)
iq = as.data.frame(iq)

# Here we are creating the z-scores, which will be added together to create a combined z-score so that we can match students on both criteria, which are weighted equally.
level = scale(readingData$level, center = TRUE, scale = TRUE)

iqLevel = as.data.frame(iq+level)
readingData = as.data.frame(cbind(id = readingData$id, iqLevel = iqLevel))
names(readingData) = c("id", "iqLevel")
readingData = as.data.frame(readingData)
# Orders the data based upon the iqLevel variable with largest number on top
readingData = readingData[order(-readingData$iqLevel),]
readingData

```
Now we need to match the students with the person below them and then pick whether the first person in each pair receives 

Could just do a sample of 1's and 2's for x people that would work, because we are saying person 1 is going to be in whatever the randomizer chooses and person two will be in the other.

Also split them up by ones and twos into two different variables.

Think about what happens when you have an odd number of particpants and you cannot pair one person.  We would have to drop one person from the analysis.  Maybe we could drop the person with the biggest difference in the matching criteria.  Or randomly assign them to a group?  Randomly assigning them to a group probably makes more sense.  

Here are we creating the particpant number.  Because the data are in order from highest score to lowest score each person is paired with the person below them.

I am grabbing the data by their particpant number and placing them side by side in seperate columns so that I can assign the first people to one intervention and make the other people paired in the other other intervention.
```{r}
# Here we are creating the numbers to assign people in sequential order
readingData$parNum = rep(c(1,2), 5)
# Here I am grabbing the people who are ones and twos and placing side by side so I can add the first person's intervention status.  We are also grabing just the id and partner number, because we do not the z-score anymore. 
parNumOne = readingData[readingData$parNum %in% c(1),]
parNumOne = parNumOne[c(1,3)]
parNumTwo = readingData[readingData$parNum %in% c(2),]
parNumTwo = parNumTwo[c(1,3)]
parNumBoth = cbind(parNumOne, parNumTwo)
names(parNumBoth) = c("id1", "parNum1", "id2", "parNum2")

#Designates which intervention person one will be assigned to and the other person will be assigned to the other
interChoice = c(1,2)
set.seed(12345)
interID = sample(interChoice, size = 5, replace = TRUE)
parNumBoth$interID1 = interID
parNumBoth
parNumBoth$interID2 = ifelse(parNumBoth$interID1 == 1, 2, 1) 

parNumBoth
```
Now we are going to drop the parNum's, because we no longer need those and append the the id values and interID, because those are what we need to determine which person does in which intervention and we are going to append the id's and interID's, and order by id's to make it clearer to select who is in what intervention.
```{r}
idTotal = cbind(id1 = parNumBoth$id1,id2 =parNumBoth$id2) 
idTotal = as.data.frame(idTotal)
idTotal = append(idTotal$id1, idTotal$id2)
idTotal = as.data.frame(idTotal)
idTotal
interIDTotal = append(parNumBoth$interID1, parNumBoth$interID2)
interIDTotal = as.data.frame(interIDTotal)
id_interID_Combine = cbind(id = idTotal,  interID =interIDTotal)

id_interID_Combine

```


