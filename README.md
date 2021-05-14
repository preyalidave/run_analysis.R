# run_analysis.R

#coursera week 4 assignment

##specifying the library

library(dplyr)

##file name

file_name <- "Coursera_DS3_Final.zip"

#downloading the file

 if (!file.exists(file_name)){
       fileURL <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
       download.file(fileURL, file_name, method="curl")
   }  
if (!file.exists("UCI HAR Dataset")) { 
       unzip(file_name) 
   }
   
   #assigning data to variables
   
 features <- read.table("UCI HAR Dataset/features.txt", col.names = c("n","functions"))
 activity <- read.table("UCI HAR Dataset/activity_labels.txt", col.names = c("code", "activity"))
 sub_test <- read.table("UCI HAR Dataset/test/subject_test.txt", col.names = "subject")
 xtest <- read.table("UCI HAR Dataset/test/X_test.txt", col.names = features$functions)
 ytest <- read.table("UCI HAR Dataset/test/y_test.txt", col.names = "code")
 sub_train <- read.table("UCI HAR Dataset/train/subject_train.txt", col.names = "subject")
 xtrain <- read.table("UCI HAR Dataset/train/X_train.txt", col.names = features$functions)
 ytrain <- read.table("UCI HAR Dataset/train/y_train.txt", col.names = "code")
 
 #merging data
 
 X <- rbind(xtrain, xtest)
 Y <- rbind(ytrain, ytest)
 subject <- rbind(sub_train, sub_test)
 merged_data <- cbind(subject, Y, X)
 
 #extracting mean and sd
 
 tidydata <- merged_data %>% select(subject, code, contains("mean"), contains("std"))
 tidydata$code <- activity[tidydata$code, 2]
 
 #activity
 
  names(tidydata)[2] = "activity"
  
  #labeling
  
 names(tidydata)<-gsub("Acc", "Accelerometer", names(tidydata))
 names(tidydata)<-gsub("Gyro", "Gyroscope", names(tidydata))
 names(tidydata)<-gsub("BodyBody", "Body", names(tidydata))
 names(tidydata)<-gsub("Mag", "Magnitude", names(tidydata))
 names(tidydata)<-gsub("^t", "Time", names(tidydata))
 names(tidydata)<-gsub("^f", "Frequency", names(tidydata))
 names(tidydata)<-gsub("tBody", "TimeBody", names(tidydata))
 names(tidydata)<-gsub("-mean()", "Mean", names(tidydata), ignore.case = TRUE)
 names(tidydata)<-gsub("-std()", "STD", names(tidydata), ignore.case = TRUE)
 names(tidydata)<-gsub("-freq()", "Frequency", names(tidydata), ignore.case = TRUE)
 names(tidydata)<-gsub("angle", "Angle", names(tidydata))
 names(tidydata)<-gsub("gravity", "Gravity", names(tidydata))
 
 #creating a second data , "tidy_data"
 
 tidy_data <- tidydata %>%
       group_by(subject, activity) %>%
       summarise_all(funs(mean))
write.table(tidy_data, "tidy_data.txt", row.name=FALSE)

#viewing content of table

View(tidy_data)
