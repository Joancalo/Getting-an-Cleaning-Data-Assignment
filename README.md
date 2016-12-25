# Getting-an-Cleaning-Data-Assignment
# Assignment for the Getting and Cleaning Data Course.
# This file explain how the Assignment was done and how the script works.

# To download the data set I used these commands 
if(!file.exists("./data")){dir.create("./data")}
zip <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
download.file(zip, destfile =  "./data/dataset.zip")
unzip("./data/dataset.zip", exdir = "./data")

# I set the wd and took a look at the files with: 
setwd("./Data")
list.files()

# I created two vectors with the activities and features names with:
activity <- read.table("./UCI HAR Dataset/activity_labels.txt")
features <- read.table("./UCI HAR Dataset/features.txt")

# I took a look at the files within the test and train dir.
list.files("./UCI HAR Dataset/test")
list.files("./UCI HAR Dataset/train")

# I created different tables reading the txt files within the "test" and "train" dir.
# This tables will be used later to join them.
test <- read.table("./UCI HAR Dataset/test/X_test.txt")
train <- read.table("./UCI HAR Dataset/train/X_train.txt")
subject_test <- read.table("./UCI HAR Dataset/test/subject_test.txt")
subject_train <- read.table("./UCI HAR Dataset/train/subject_train.txt")
activity_test <- read.table("./UCI HAR Dataset/test/y_test.txt")
activity_train <- read.table("./UCI HAR Dataset/train/y_train.txt")

# I created a vector of the features names and changed the names of the variables in the tables subject_test, subject_train, 
# activity_test y activity_train to subject and activity respectively.     
nms <- as.vector(features$V2)
colnames(subject_test) <- c("subject")
colnames(subject_train) <- c("subject")
colnames(activity_test) <- c("activity")
colnames(activity_train) <- c("activity")

# To appropriately label the data sets with descriptive variable names I used colnames() and the vector nms previously created:
colnames(test) <- nms
colnames(train) <- nms

# I created new data frames (df_test and df_train) with cbibd() joining the tables subject_test, activity_test and test, 
# and subject_train, activity_train and  train 
df_test <- cbind(subject_test, activity_test, test)
df_train <- cbind(subject_train, activity_train, train)

# To merge the training and the test sets I created a final data frame putting together the last two df with rbind()
fnl_df <- rbind(df_test, df_train)

# To extract only the measurements on the mean and standard deviation for each measurement I generated a vector of the last df 
# variable's names and extracted the ones of interest with grep(). Finally I selected the variables of interest from fnl_df into a new 
# df called mean_std_df  
fnl_df_nms <- as.vector(names(fnl_df))
mean_std_nms <- grep("subject|activity|mean|std", fnl_df_nms, value = TRUE)
mean_std_df <- fnl_df[,mean_std_nms]

# To use descriptive activity names to name the activities in the new data set I used these commands to modify the values of the 
# activity variable from 1,2,3,4,5 and 6 to WALKING, WALKING UPSTAIRS, WALKING DOWNSTAIRS, SITTING, STANDING and LAYING :
mean_std_df$activity[mean_std_df$activity == 1] <- "WALKING"
mean_std_df$activity[mean_std_df$activity == 2] <- "WALKING UPSTAIRS"
mean_std_df$activity[mean_std_df$activity == 3] <- "WALKING DOWNSTAIRS"
mean_std_df$activity[mean_std_df$activity == 4] <- "SITTING"
mean_std_df$activity[mean_std_df$activity == 5] <- "STANDING"
mean_std_df$activity[mean_std_df$activity == 6] <- "LAYING"

# To create an independent tidy data set with the average of each variable for each activity and each subject I first arranged the 
# mean_std_df by the subject and activity variables. Then, I reshaped the df with melt()indicating the id variable and the measurement 
# variables. Finally, with dcast() I was able to generate a df of averages for each activity and variable for each subject.       
library(dplyr)
mean_std_df<- arrange(mean_std_df, subject, desc(activity))
library(reshape2)
mean_std_dfMelt<- melt(mean_std_df, id=c("subject","activity"), measure.vars = mean_std_nms[3:81])
mean_std_dfCast <- dcast(mean_std_dfMelt, subject + activity~variable, mean)

# I wrote the final average's df into a file with write.table()
write.table(mean_std_dfCast, file = "./mean_std_dfCast.txt", row.name=FALSE)


