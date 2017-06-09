---
title: "Paired Randomization Example"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```
Below is an example of how to randomly pair individuals together who have similar scores based upon two criteria (e.g. reading age and IQ).  The first step is to create an artificial dataset.  The next step is to turn the criteria, in this case that is iq and level, into z-scores.  Transforming the pairing criteria variables into z-scores put them on the same scale.  With pairing criteria variables on the same scale, we can combine them creating one criteria that the researchers can pair students with like criteria scores.  To transform the criteria, we used the scale function with the center and scale options set to TRUE.  We created the single pairing criteria variable by adding the two criteria variables iq and level into one criteria variable iqLevel.  Next, we combined the new pairing criteria variable with the id variable allowing us to identify which person received which pairing criteria score.  Finally, we ordered the dataset by the largest paring criteria.    
```{r}
set.seed(12345)
readingData = data.frame(id = c(1:10), iq = rnorm(10), level = rnorm(10))
readingData = as.data.frame(readingData)

iq = scale(readingData$iq , center = TRUE, scale = TRUE)
iq = as.data.frame(iq)
level = scale(readingData$level, center = TRUE, scale = TRUE)
iqLevel = as.data.frame(iq+level)

readingData = as.data.frame(cbind(id = readingData$id, iqLevel = iqLevel))
names(readingData) = c("id", "iqLevel")
readingData = as.data.frame(readingData)

readingData = readingData[order(-readingData$iqLevel),]
readingData
```
With the data set ordered by largest pairing criteria score, we can then pair each person with the person directly below them, because the person below the person above them is the person that each person above is closest to.  To identify the pairs, we created a parNum or participant number variable to identify if the person was the first or second person in the pair.  

Then we grabbed each of the participants by their pairing number and placed them into their own columns.  We placed the participants in columns by their pairing numbers, so that we could randomly assign one person in the pair to one condition and the other the opposite.  To assign one pair member to one intervention and the other to the opposite, we created a variable called interChoice which contained the numbers one and two.  We then randomly sampled from that variable five times (i.e. there are five first pair members in this example) with replacement and assigned each first pair members to the condition that the random sampling produced called interID1.  Then we created an ifelse statement that created a variable giving the other pair the opposite condition and called interID2.
```{r}
readingData$parNum = rep(c(1,2), 5)

parNumOne = readingData[readingData$parNum %in% c(1),]
parNumOne = parNumOne[c(1,3)]
parNumTwo = readingData[readingData$parNum %in% c(2),]
parNumTwo = parNumTwo[c(1,3)]
parNumBoth = cbind(parNumOne, parNumTwo)
names(parNumBoth) = c("id1", "parNum1", "id2", "parNum2")
head(parNumBoth)

interChoice = c(1,2)
set.seed(12345)
interID = sample(interChoice, size = 5, replace = TRUE)
parNumBoth$interID1 = interID
parNumBoth$interID2 = ifelse(parNumBoth$interID1 == 1, 2, 1) 
parNumBoth
```
Finally, we cleaned the data set by appending the two pair's ids and interIDs together creating two variables id and idInter identifying the participant’s id and intervention assignment in order from first id to last.   
```{r}
idTotal = cbind(id1 = parNumBoth$id1,id2 =parNumBoth$id2) 
idTotal = as.data.frame(idTotal)
idTotal = append(idTotal$id1, idTotal$id2)
idTotal = as.data.frame(idTotal)
interIDTotal = append(parNumBoth$interID1, parNumBoth$interID2)
interIDTotal = as.data.frame(interIDTotal)
id_interID_Combine = cbind(id = idTotal,  interID =interIDTotal)
names(id_interID_Combine) = c("id", "interID")
id_interID_Combine = as.data.frame(id_interID_Combine)
id_interID_Combine = id_interID_Combine[order(id_interID_Combine$id),] 
id_interID_Combine
```


