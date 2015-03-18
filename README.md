---
title: "README"
output: html_document
---

step 1: read in data, and join the subject and label files with the data set for test and train separately
```
library(dplyr)
actlabel<-read.table("./activity_labels.txt")
test<-read.table("./test/X_test.txt")
test_label=read.table("./test/y_test.txt")
test_subject<-read.table("./test/subject_test.txt")
test<-cbind(test,test_label,test_subject)
train<-read.table("./train/X_train.txt")
train_label=read.table("./train/y_train.txt")
train_subject<-read.table("./train/subject_train.txt")
train<-cbind(train,train_label,train_subject)
```

step 2: Merges the training and the test sets to create one data set.
```
all<-rbind(test,train)
```

step 3: read in measures table, and select only variable columns from the merged table with "mean" or "std" in variable names
```
features<-read.table("./features.txt")
x<-grep("mean",features[,2])
y<-grep("std",features[,2])
index<-c(x,y)
selection<-all[,c(index,562,563)]
```

step 4: Uses descriptive activity names to name the activities in the data set
```
actlabel<-rename(actlabel,V1.1=V1,activityLabel=V2)
selection_2<-join(selection,actlabel)
```

step 5: Appropriately labels the data set with descriptive variable names. 
```
var_names<-names(selection_2[,1:79])
var_index<-as.numeric(sub("V","",var_names))
colnames(selection_2)[1:79]<-as.character(features[var_index,2])
selection_2<-rename(selection_2,Subject=V1.2)
selection_2<-selection_2[,-80]
```


step 6: analysis, and creates a second, independent tidy data set with the average of each variable for each activity and each subject.
```
library(reshape2)
melted<-melt(selection_2,id.vars=c("Subject","activityLabel"))
grouped <-group_by(melted,activityLabel,Subject,variable)
summary<-summarize(grouped,mean=mean(value))
write.table(summary,file="./summary.txt",row.name=FALSE)

```


        
        
        



