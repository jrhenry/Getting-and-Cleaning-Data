---
title: "README"
author: "James Henry"
date: "November 2, 2017"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

## R SCRIPT

```{r}

library(tidyverse)

# Looks for the dataset folder UCI HAR Dataset
# If it does not exist it will download, extract, and delete the zipfile

if (!file.exists("UCI HAR Dataset")){
      dir.create("data")
      fileURL <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
      download.file(fileURL, destfile = "./data/phone.zip")
      unzip("./data/phone.zip")
      unlink("data", recursive = T)
}

# Changes working directory to the UCI HAR Dataset folder
oldwd <- getwd()
setwd("./UCI HAR Dataset")

# Reads in all parts of the test and train data sets and provides variable names
# for the columns. The test data variable names are from the features.txt file.
# This accomplishes Step 4 of the assignment.
features <- read.table("./features.txt", quote = "\"", comment.char = "", 
                       stringsAsFactors = F)
cnames <- gsub("-",".", sub("\\()","",as.array(features$V2)))
test <- read.table("./test/X_test.txt", col.names = cnames)
testsub <- read.table("./test/subject_test.txt", col.names = "subject")
testact <- read.table("./test/y_test.txt", col.names = "activity")
train <- read.table("./train/X_train.txt", col.names = cnames)
trainsub <- read.table("./train/subject_train.txt", col.names = "subject")
trainact <- read.table("./train/y_train.txt", col.names = "activity")

# Merges three parts of each test and train data set
test <- cbind(test, testsub, testact)
train <- cbind(train, trainsub, trainact)

# Merges the train and test data sets
# This accomplishes Step 1 of the assignment.
mergeddata <- rbind(train, test)

# Extracts only the meansurements on the mean and standard deviation for 
# each measurement.
# This accomplishes Step 2 of the assignment.
mergeddata <- mergeddata[,grep("mean|std|activity|subject",names(mergeddata))]

# Gives descriptive activity names to the activities in the data set.
# This accomplishes Step 3 of the assignment.
activities <- read.table("activity_labels.txt")
activities <- as.array(activities$V2)
mergeddata$activity <- factor(mergeddata$activity, labels = activities)

# Removes unnecessary data
rm(features, cnames, test, testact, testsub, train, trainact, trainsub)

# Creates a second, independent tidy data set with the average of each variable
# for each activity and each subject from the mergeddata data set.
# This accomplishes Step 5 of the assignment.
activitysubjectaverage <- summarize_all(group_by(mergeddata, activity, subject),
                                        funs(mean))
setwd(oldwd)
write.table(activitysubjectaverage,"Average Phone Data.txt", row.names = FALSE)

```
