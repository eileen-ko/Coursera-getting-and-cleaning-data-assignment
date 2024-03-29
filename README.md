The repo includes the following files:
=========================================

- 'README.txt'

- 'tidydata.txt': the output of the assignment.

- 'CodeBook': the file describing the variables.

=================================
## step 1: import dataset

features <- read.table("~/Downloads/UCI HAR Dataset/features.txt", quote="\"", comment.char="", col.names = c(" ", "feature"))

activities <- read.table("~/Downloads/UCI HAR Dataset/activity_labels.txt", quote="\"", comment.char="", col.names = c("code", "activities"))


## combine test data

x_test <- read.table("X_test.txt")

x_test <- read.table("X_test.txt", col.names = features$feature)

y_test <- read.table("y_test.txt", col.names = "code")

subject_test <- read.table("subject_test.txt", col.names = "subject")

test <- cbind(x_test, y_test, subject_test)


## combine train data
x_train <- read.table("X_train.txt", col.names = features$feature)

y_train <- read.table("y_train.txt", col.names = "code")

subject_train <- read.table("subject_train.txt", col.names = "subject")

train <- cbind(x_train, y_train, subject_train)


## Merges the training and the test sets to create one data set

data <- rbind(test, train)


## Extracts the mean and standard deviation measurement

selecteddata <- data %>% select(subject, code, contains("mean"), contains("std"))

selecteddata$code <- activities[selecteddata$code, 2]


## labels the data set with descriptive variable names


names(selecteddata) <- gsub("BodyBody", "Body", names(selecteddata))

names(selecteddata)<-gsub("^t", "Time", names(selecteddata))

names(selecteddata)<-gsub("^f", "Frequency", names(selecteddata))


## creates a second, independent tidy data set with the average of each variable for each activity and each subject

## change the names of selected data

names(selecteddata)[2] <- "activity"


## group the dataset by "subject" and "activity"

tidydata <- selecteddata %>%
    group_by(subject, activity) %>%
    summarise_all(funs(mean))


write.table(tidydata, "tidydata.txt", row.name=FALSE)