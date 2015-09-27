Practical Machine Learning Course - Project

Manjula Sridhar

27th September, 2015

Scripts have been produced and tested on RStudio Version 3.2.1 and Windows 7.

Background

Using devices such as Jawbone Up, Nike FuelBand, and Fitbit it is now possible to collect a large amount of data about personal activity relatively inexpensively. These type of devices are part of the quantified self movement - a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. One thing that people regularly do is quantify how much of a particular activity they do, but they rarely quantify how well they do it. In this project, your goal will be to use data from accelerometers on the belt, forearm, arm, and dumbell of 6 participants. They were asked to perform barbell lifts correctly and incorrectly in 5 different ways. More information is available from the website here: http://groupware.les.inf.puc-rio.br/har (see the section on the Weight Lifting Exercise Dataset).

Data

The training data for this project are available here: [https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv]
The test data are available here: [https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv]
The data for this project come from this source: [http://groupware.les.inf.puc-rio.br/har]. If you use the document you create for this class for any purpose please cite them as they have been very generous in allowing their data to be used for this kind of assignment.

Preliminary Work

Initial steps

Initially, seed was set at 687 randomly. 
Packages such as caret and randomForest, rpart and rpart.plot were downloaded and installed.

Procedure for model

Our outcome variable is classe, a factor variable with 5 levels:
1.Class A - exactly according to the specification
2.Class B - throwing the elbows to the front
3.Class C - lifting the dumbbell only halfway
4.Class D - lowering the dumbbell only halfway
5.Class E - throwing the hips to the front
Prediction evaluations is based on maximizing the accuracy and minimizing the out-of-sample error. All other available variables after cleaning is used for prediction.
Two models are tested using decision tree and random forest algorithms. The model with the highest accuracy is chosen as our final model.

Cross-validation

Cross-validation is performed by subsampling our training data set randomly without replacement into 2 subsamples: 
sampletrain data (75% of the original Training data set) and 
sampletest data (25%). 

Procedure

Features with missing values are discarded as they are irrelevant. All other features will be kept as relevant variables.
Algorithms used: Decision tree and random forest algorithms 
Code and Results
#install and load required libraries
library(caret)
library(randomForest) 
library(rpart) 
library(rpart.plot)

#set seed
set.seed(687)
# Loading the training data set into my R session replacing all missing with "NA"
train <- read.csv("C:/Users/Administrator/Desktop/pml-training.csv", na.strings=c("NA","#DIV/0!", ""))

# Loading the testing data set 
test <- read.csv('C:/Users/Administrator/Desktop/pml-testing.csv', na.strings=c("NA","#DIV/0!", ""))

# Delete columns with all missing values
train<-train[,colSums(is.na(train)) == 0]
test <-test[,colSums(is.na(test)) == 0]

# remove unnecessary variables like user_name, raw_timestamp_part_1, raw_timestamp_part_,2 cvtd_timestamp, new_window, and  num_window 
train   <-train[,-c(1:7)]
test <-test[,-c(1:7)]

subsamples <- createDataPartition(y=train$classe, p=0.75, list=FALSE)
sampletrain<- train[subsamples, ] 
sampletest <- train[-subsamples, ]

plot(sampletrain$classe, col="green", main="Bar Plot of levels of the variable classe of dataset", xlab="classe levels", ylab="Frequency")


solution1 <- rpart(classe ~ ., data=subTraining, method="class")

# Predicting:
pred1 <- predict(solution1, sampletest, type = "class")

# Plot of the Decision Tree
rpart.plot(solution1, main="Classification Tree", extra=102, under=TRUE, faclen=0)

# Test results on our sampletest data set:
confusionMatrix(pred1, sampletest$classe)

#Solution in random forest
solution2 <- randomForest(classe ~. , data=subTraining, method="class")

# Predicting:
pred2 <- predict(solution2, sampletest, type = "class")

# Test results on sampletest data set:
confusionMatrix(pred2, sampletest$classe)

# predict outcome levels on the original Testing data set using Random Forest algorithm
final<- predict(solution2, test, type="class")
final


# Write files
file_write = function(x){
  n = length(x)
  for(i in 1:n){
    filename = paste0("problem_id_",i,".txt")
    write.table(x[i],file=filename,quote=FALSE,row.names=FALSE,col.names=FALSE)
  }
}

file_write(final)

