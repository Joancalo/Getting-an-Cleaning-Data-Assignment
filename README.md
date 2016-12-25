# Getting-an-Cleaning-Data-Assignment
Assignment for the Getting and Cleaning Data Course.
This file explain how the Assignment was done and how the script works.

To download the data set I used these commands 
if(!file.exists("./data")){dir.create("./data")}
zip <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
download.file(zip, destfile =  "./data/dataset.zip")
unzip("./data/dataset.zip", exdir = "./data")

I set the wd and took a look at the files with: 
setwd("./Data")
list.files()

I created two vectors with the activities and features names with:
activity <- read.table("./UCI HAR Dataset/activity_labels.txt")
features <- read.table("./UCI HAR Dataset/features.txt")

I took a look at the files within the test and train dir.
list.files("./UCI HAR Dataset/test")
list.files("./UCI HAR Dataset/train")

I created different tables reading the txt files within the "test" and "train" dir.
This tables will be used later to join them.
test <- read.table("./UCI HAR Dataset/test/X_test.txt")
train <- read.table("./UCI HAR Dataset/train/X_train.txt")
subject_test <- read.table("./UCI HAR Dataset/test/subject_test.txt")
subject_train <- read.table("./UCI HAR Dataset/train/subject_train.txt")
activity_test <- read.table("./UCI HAR Dataset/test/y_test.txt")
activity_train <- read.table("./UCI HAR Dataset/train/y_train.txt")

I cre
nms <- as.vector(features$V2)
colnames(subject_test) <- c("subject")
colnames(subject_train) <- c("subject")
colnames(activity_test) <- c("activity")
colnames(activity_train) <- c("activity")
colnames(test) <- nms
df_test <- cbind(subject_test, activity_test, test)
colnames(train) <- nms
df_train <- cbind(subject_train, activity_train, train)
fnl_df <- rbind(df_test, df_train)
fnl_df_nms <- as.vector(names(fnl_df))
mean_std_nms <- grep("subject|activity|mean|std", fnl_df_nms)
mean_std_df <- fnl_df[,mean_std_nms]
mean_std_df$activity[mean_std_df$activity == 1] <- "WALKING"
mean_std_df$activity[mean_std_df$activity == 2] <- "WALKING UPSTAIRS"
mean_std_df$activity[mean_std_df$activity == 3] <- "WALKING DOWNSTAIRS"
mean_std_df$activity[mean_std_df$activity == 4] <- "SITTING"
mean_std_df$activity[mean_std_df$activity == 5] <- "STANDING"
mean_std_df$activity[mean_std_df$activity == 6] <- "LAYING"
mean_std_df<- arrange(mean_std_df, subject, desc(activity))
mean_std_dfMelt<- melt(mean_std_df, id=c("subject","activity"), measure.vars = mean_std_df_nms[3:81])
mean_std_dfCast <- dcast(mean_std_dfMelt, subject + activity~variable, mean)

write.table(mean_std_dfCast, row.name=FALSE)

write.table(mean_std_dfCast, file = "C:/Users/JoseA/Documents/Coursera/Getting and Cleaning Data/Week 4/Assignment/mean_std_dfCast.txt", row.name=FALSE)


