# DataCleaning
Coursera Data Cleaning class project

Below is the code and explanations for the class project:

## 1:  Merges the training and the test sets to create one data set.

## Get the train data, add train_y
train <- read.table("X_train.txt")
train_y <- read.table("y_train.txt")
train_subject <- read.table("subject_train.txt")

trains <- cbind(train_subject, train_y, train)

## Get the test data, add test_y
test <- read.table("X_test.txt")
test_y <- read.table("y_test.txt")
test_subject <- read.table("subject_test.txt")

tests <- cbind(test_subject, test_y, test)

## stack tests and trains
data <- rbind(trains, tests)

## 2:  Extracts only the measurements on the mean and standard deviation for each measurement. 

## Get feature names for column labels
features <- read.table("features.txt")

## change data names to features vector and replace names
names(data) <- c("Subject", "Activity", as.character(features[,2]))

## extract cols with Mean, STD but keep first two columns (activity & subject)
cols <- grep(c("mean", "std") , names(data), value = TRUE)
data_r <- cbind(data[,1:2], data[, cols])

## 3:  Uses descriptive activity names to name the activities in the data set

## merge in the activity names to the rows
cleaned <- merge(activities, data_r, by.x="V1", by.y="Activity", all=TRUE)

## 4:  Appropriately labels the data set with descriptive variable names.

## Add Activity names and replace other names
named<- names(cleaned[,3:49])
names(cleaned) <- c("ActivityID", "Activity", named)

## remove ActivityID column from cleaned
cleaned <- cleaned[2:49]

## write out cleaned data set
## write.table(cleaned, "merged_clean_data.txt")

## 5:  Creates a second, independent tidy data set with the average of each variable for each activity and each subject.

## melt data with 2 factors:  Activity and Subject, use column name vector as vars
melted <- melt(cleaned, id=c("Activity", "Subject"), measure.vars=c(names(cleaned[3:48])))

## Calculate Means by Subject for each Activity using dcast
## add a 'means' indicator on each row to identify the function
## Reset all column names

no5_tidy_means <- dcast(melted, Subject + Activity ~ variable, mean)
no5_tidy_means[,49] <- "mean"
names(no5_tidy_means) <- c("Activity", named, "Function")

## Calculate StDev by Subject for each Activity using dcast
## add a 'St_Dev' indicator on each row to identify the function
## Reset all column names

no5_tidy_sd <- dcast(melted, Subject + Activity ~ variable, sd)
no5_tidy_sd[,49] <- "std_dev"
names(no5_tidy_sd) <- c("Activity", named, "Function")

## rbind the 'means' colums above the 'st_dev' columns

no5_tidy <- rbind(no5_tidy_means, no5_tidy_sd)

## Output
write.table(no5_tidy_means, "no5_tidy_means_output.txt")




