# DataScienceCourseProject
Coursera Getting and Cleaning Data Course Project

When executed, this script will download the data collected from the accelerometers from the Samsung Galaxy S smartphone generated in the Human Activity Recognition Using Smartphones Data Set

Once downloaded, the script will do the following:

1. Merge the training and the test sets to create one data set
2. Extract the measurements on the mean and standard deviation for each measurement
3. Use descriptive activity names to name the activities in the data set
4. Appropriately label the data set with descriptive variable name
5. Create a new, independent tidy data set with the average of each variable for each activity and each subject - New data set name is TidyData.txt
 

## Set working directory (left out for security)
```
datafileUrl <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
download.file(datafileUrl, destfile="ProjectData.zip", method="curl")

unzip(zipfile="ProjectData.zip")

list.files(dataPath, recursive=TRUE)
dataPath <- "UCI HAR Dataset"
```
Navigate to working directory to test data and create datasets
```
setwd(file.path(dataPath, "test"))
list.files()
##[1] "Inertial Signals" "subject_test.txt" "X_test.txt"          "y_test.txt"      

data_subject_test<-read.table("subject_test.txt", header=FALSE)
data_features_test<-read.table("x_test.txt", header=FALSE)
data_activity_test<-read.table("y_test.txt", header=FALSE)
```
Navigate to working directory to train data and create datasets
```
setwd("../train")
list.files()
##[1] "Inertial Signals"  "subject_train.txt" "X_train.txt"       "y_train.txt"      

data_subject_train <- read.table("subject_train.txt", header=FALSE)
data_features_train <- read.table("X_train.txt", header=FALSE)
data_activity_train <- read.table("y_train.txt", header=FALSE)
```
##  Merge data sets to create 1 data set
Combine dataframes with like rows
```
dataFeatures <- rbind(data_features_train, data_features_test)
dataSubject <- rbind(data_subject_train, data_subject_test)
dataActivity <- rbind(data_activity_train, data_activity_test)
```
## Name columns appropriately
```
setwd("../")
dataFeaturesNames <- read.table("features.txt", header=FALSE)
names(dataFeatures) <- dataFeaturesNames$V2
names(dataSubject) <- c("Subject")
names(dataActivity) <- c("Activity")

dataSet <- cbind(dataSubject, dataActivity, dataFeatures)
```

## Get subset of data column names for std() or mean()
Use grep() to find matching column names
http://www.freeformatter.com/regex-tester.html
Learn the hard way that R uses \\ rather than \ to escape
```
dataSubsetNames <- dataFeaturesNames$V2[grep("mean\\(\\)|std\\(\\)", dataFeaturesNames$V2)]
dataSubsetNames <- as.character(dataSubsetNames)
dataSubsetNames <- c(dataSubsetNames, "Subject", "Activity")

dataSubset <- subset(dataSet,select=dataSubsetNames)
```
## Update descriptive activity names to rename columns
prefix t = Time (noted in features_info.txt)
prefix f = Frequency (noted in features_info.txt)
Acc = Acceleration (noted in features_info.txt)
Gyro = Gyroscope (noted in features_info.txt)
Jerk = Leave as is
Mag = Magnitude (noted in features_info.txt)
```
names(dataSubset) <- gsub("^t", "Time", names(dataSubset))
names(dataSubset) <- gsub("^f", "Frequency", names(dataSubset))
names(dataSubset) <- gsub("Acc", "Acceleration", names(dataSubset))
names(dataSubset) <- gsub("Gyro", "Gyroscope", names(dataSubset))
names(dataSubset) <- gsub("Mag", "Magnitude", names(dataSubset))
```
## Create an average of values for each subject and activity
```
tidyData <- aggregate(.~ Subject * Activity, data=dataSubset, FUN=mean)
```
## Save data to txt file
```
write.table(tidyData,"TidyData.txt", row.name=FALSE)
```
