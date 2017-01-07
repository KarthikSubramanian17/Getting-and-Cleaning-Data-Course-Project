# Getting and Cleaning Data Course Project- Coursera John Hopkins Data Science Course

> The purpose of this project is to demonstrate your ability to collect, work with, and clean a data set. The goal is to prepare tidy data that can be used for later analysis. You will be graded by your peers on a series of yes/no questions related to the project.You will be required to submit:
>
1. a tidy data set as described below
2. a link to a Github repository with your script for performing the analysis
3. codeBook.md that describes the variables, the data, and any work that you performed to clean up the data 
4. README.md that explains how all of the scripts work and how they are connected.  
>
> One of the most exciting areas in all of data science right now is wearable computing - see for example this article . Companies like Fitbit, Nike, and Jawbone Up are racing to develop the most advanced algorithms to attract new users. The data linked to from the course website represent data collected from the accelerometers from the Samsung Galaxy S smartphone. A full description is available at the site where the data was obtained: 
> 
> http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones 

> Here are the data for the project: 

> https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip 
> 
> You should create one R script called run_analysis.R that does the following. 
> 
> 1. Merges the training and the test sets to create one data set.
> 2. Extracts only the measurements on the mean and standard deviation for each measurement.
> 3. Uses descriptive activity names to name the activities in the data set.
> 4. Appropriately labels the data set with descriptive activity names.
> 5. Creates a second, independent tidy data set with the average of each variable for each activity and each subject. 
> 
> Good luck!

# Code explanations

*We applied all same read format to the files. We used __sep=""__ cause given file format seperated like that. And also __header=FALSE__, just dont want to lose first row of data. If its true, first row would be column names which we dont want to.

Reads these two file from **UCI HAR Dataset** and takes sensitive data. For activitiy labels first column includes rownumber which is unneccesary. 
```R 
   ##Reading Features and ActivityLabels vector
   features <- read.csv("features.txt", sep = "", header = FALSE)[2]
   activities <- read.csv("activity_labels.txt", sep = "", header = FALSE)
```

Again reads from same location and combine test and train set with __rbind__ function.
```R
   ##Reading Sets
   testSet <- read.csv("test/X_test.txt", sep = "", header = FALSE)
   trainSet <- read.csv("train/X_train.txt", sep = "", header = FALSE)
   mergedSet <- rbind(testSet,trainSet)        
```   
Same exact things with previous step
```R
   ##Reading Movement
   testMoves <- read.csv("test/Y_test.txt", sep = "", header = FALSE)
   trainMoves <- read.csv("train/Y_train.txt", sep = "", header = FALSE)
   mergedMoves <- rbind(testMoves, trainMoves)
      
   ##Reading PersonID
   testPerson <- read.csv("test/subject_test.txt", sep = "", header = FALSE)
   trainPerson <- read.csv("train/subject_train.txt", sep = "", header = FALSE)
   mergedPerson <- rbind(testPerson, trainPerson)
```
Assigns real column attributes(decriptive column names) that is kept in features vector to mergedSet we have formed in previous steps. After that, select all columns that key values passing through this attributes
```R
   ##Extracting columns which includes measurements
   names(mergedSet) <- features[ ,1]
   mergedSet <- mergedSet[ grepl("std|mean", names(mergedSet), ignore.case = TRUE) ] 
```
Descriptive values for activity columns.
```R
   #Descriptive ActivityName analysis
   mergedMoves <- merge(mergedMoves, activities, by.x = "V1", by.y = "V1")[2]
   mergedSet <- cbind(mergedPerson, mergedMoves, mergedSet)
   names(mergedSet)[1:2] <- c("PersonID", "Activities")
```

Tidying set according to personID and activities
```R
   ##Tidying mergedSet
   group_by(mergedSet, PersonID, Activities) %>%
         summarise_each(funs(mean))
```            
==================================================================
Human Activity Recognition Using Smartphones Dataset
Version 1.0
==================================================================
Jorge L. Reyes-Ortiz, Davide Anguita, Alessandro Ghio, Luca Oneto.
Smartlab - Non Linear Complex Systems Laboratory
DITEN - Università degli Studi di Genova.
Via Opera Pia 11A, I-16145, Genoa, Italy.
activityrecognition@smartlab.ws
www.smartlab.ws
==================================================================

The experiments have been carried out with a group of 30 volunteers within an age bracket of 19-48 years. Each person performed six activities (WALKING, WALKING_UPSTAIRS, WALKING_DOWNSTAIRS, SITTING, STANDING, LAYING) wearing a smartphone (Samsung Galaxy S II) on the waist. Using its embedded accelerometer and gyroscope, we captured 3-axial linear acceleration and 3-axial angular velocity at a constant rate of 50Hz. The experiments have been video-recorded to label the data manually. The obtained dataset has been randomly partitioned into two sets, where 70% of the volunteers was selected for generating the training data and 30% the test data. 

The sensor signals (accelerometer and gyroscope) were pre-processed by applying noise filters and then sampled in fixed-width sliding windows of 2.56 sec and 50% overlap (128 readings/window). The sensor acceleration signal, which has gravitational and body motion components, was separated using a Butterworth low-pass filter into body acceleration and gravity. The gravitational force is assumed to have only low frequency components, therefore a filter with 0.3 Hz cutoff frequency was used. From each window, a vector of features was obtained by calculating variables from the time and frequency domain. See 'features_info.txt' for more details. 

For each record it is provided:
======================================

- Triaxial acceleration from the accelerometer (total acceleration) and the estimated body acceleration.
- Triaxial Angular velocity from the gyroscope. 
- A 561-feature vector with time and frequency domain variables. 
- Its activity label. 
- An identifier of the subject who carried out the experiment.

The dataset includes the following files:
=========================================

- 'README.txt'

- 'features_info.txt': Shows information about the variables used on the feature vector.

- 'features.txt': List of all features.

- 'activity_labels.txt': Links the class labels with their activity name.

- 'train/X_train.txt': Training set.

- 'train/y_train.txt': Training labels.

- 'test/X_test.txt': Test set.

- 'test/y_test.txt': Test labels.

The following files are available for the train and test data. Their descriptions are equivalent. 

- 'train/subject_train.txt': Each row identifies the subject who performed the activity for each window sample. Its range is from 1 to 30. 

- 'train/Inertial Signals/total_acc_x_train.txt': The acceleration signal from the smartphone accelerometer X axis in standard gravity units 'g'. Every row shows a 128 element vector. The same description applies for the 'total_acc_x_train.txt' and 'total_acc_z_train.txt' files for the Y and Z axis. 

- 'train/Inertial Signals/body_acc_x_train.txt': The body acceleration signal obtained by subtracting the gravity from the total acceleration. 

- 'train/Inertial Signals/body_gyro_x_train.txt': The angular velocity vector measured by the gyroscope for each window sample. The units are radians/second. 

Notes: 
======
- Features are normalized and bounded within [-1,1].
- Each feature vector is a row on the text file.

For more information about this dataset contact: activityrecognition@smartlab.ws

License:
========
Use of this dataset in publications must be acknowledged by referencing the following publication [1] 

[1] Davide Anguita, Alessandro Ghio, Luca Oneto, Xavier Parra and Jorge L. Reyes-Ortiz. Human Activity Recognition on Smartphones using a Multiclass Hardware-Friendly Support Vector Machine. International Workshop of Ambient Assisted Living (IWAAL 2012). Vitoria-Gasteiz, Spain. Dec 2012

This dataset is distributed AS-IS and no responsibility implied or explicit can be addressed to the authors or their institutions for its use or misuse. Any commercial use is prohibited.

Jorge L. Reyes-Ortiz, Alessandro Ghio, Luca Oneto, Davide Anguita. November 2012.
