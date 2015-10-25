# Getting_and_Cleaning_Data_Project
The purpose of this project is to demonstrate your ability to collect, work with, and clean a data set.


require(data.table)
require(dplyr)

# Set current working directoty 
setwd("C:/R/Get and Clean data/Assignment")

# Remove all objects
rm(list = ls())

## Task 1: Merges the training and the test sets to create one data set.

    # Read test set from X_test.txt
    testset <- read.table("Data/UCI HAR Dataset/test/X_test.txt")
    
    # Read train sets from X_train.txt
    trainset <- read.table("Data/UCI HAR Dataset/train/X_train.txt")
    
    # combine test and train sets to create one data set using rbind
    dataset <- rbind(testset,trainset)
    
    rm(testset,trainset)
## Finish task 1
    
    
## Task 2: Extracts only the measurements on the mean and standard deviation for each measurement. 
    
    # Load measurement features from features.txt files
    features <- data.table(read.table("Data/UCI HAR Dataset/features.txt"))
    
    # Only the measurement on mean and standard deviation is selected
    features <- features[features$V2 %like% "mean" |features$V2 %like% "std" ]
    
    # using variable V1 in selectefeatures as selected column indices 
    dataset <- dataset[,features$V1]

    #Appropriately labels the data set with descriptive variable names.(see task 4)
    colnames(dataset) = features$V2
        
    rm(features)
## Finish task 2
    
        
## Task 3: Uses descriptive activity names to name the activities in the data set
    
    # Load Label ID for test and train sets and combine into one dataLabel set
    testLabel <- read.table("Data/UCI HAR Dataset/test/Y_test.txt")
    trainLabel <- read.table("Data/UCI HAR Dataset/train/Y_train.txt")
    datalabel <- rbind(testLabel,trainLabel)
    rm(testLabel,trainLabel)
    
    # Load activity lables from .csv files
    activity_label <- read.table("Data/UCI HAR Dataset/activity_labels.txt")

    # Merging datalable and activity lables to get the names of activity in dataset
    data_activity <- merge(datalabel,activity_label,by = "V1")
    names(data_activity) <- c("Activity_ID","Activity_name")
    rm(datalabel,activity_label)
    
    # Load subject ID for test and train sets and combine using rbind
    testSubject <- read.table("Data/UCI HAR Dataset/test/subject_test.txt")
    trainSubject <- read.table("Data/UCI HAR Dataset/train/subject_train.txt")
    data_subject <- rbind(testSubject,trainSubject)
    colnames(data_subject) <- c("Subject_ID")
    rm(testSubject,trainSubject)
    
    # combine data_subject, data_acticity_label and dataset to create one data set using cbind
    dataset = cbind(data_subject,data_activity,dataset)
    rm(data_subject,data_activity)
## Finish task 3    
    

    
## Task 4: Appropriately labels the data set with descriptive variable names.
    # this has been done in task 2    
    
## Finish task 4    
    


## Task 5: From the data set in step 4, creates a second, independent tidy data set 
##         with the average of each variable for each activity and each subject..
    index <- dataset %>% select(Subject_ID,Activity_name) %>% unique()
    
    for (i in c(1:nrow(index)) ) {
      
        datasubset <- dataset[which(dataset$Subject_ID == index$Subject_ID[i] & 
                                    dataset$Activity_name == index$Activity_name[i]),]
        
        datasubset <- select(datasubset,-c(1:3))
        
        if (i == 1 ) {
            tidydataset <- colMeans(datasubset)     
        } else { 
            tidydataset <- rbind(tidydataset,colMeans(datasubset))     
        }
      
    }
    
    tidydataset <- cbind(index,tidydataset)    
    
## Finish task 5    
 
    rm(i,index,datasubset)
