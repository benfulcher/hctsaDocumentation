# Exploring classification rate using `TS_Classify`

When performing a time-series classification task, a basic first exploration of the data is to investigate how accurately a classifier can performing using all of the features computed in your *hctsa* analysis.
This can be done by running `TS_Classify`.
The function requires that group labels be assigned to the time series, using `TS_LabelGroups`.
For example, running the function on a dataset with three classes of five time series each, as `TS_Classify('norm')`, produces:
```
    Classification rate (3-class) using 5-fold svm classification with 8192 features:
    73.333 +/- 14.907%
```
In this case, the function has attempted to learn a linear svm classifier on the features to predict the labels assigned to the data, using 5-fold cross validation (note that the default is 10-fold, but for smaller datasets such as this one, fewer folds are used automatically).
The results show that using 8192 features we obtain a mean classification accuracy of 73.3% (with a standard deviation over the 5-folds of 14.9%).

You can set the classifier with the second input, as well as a range of other options, including computing the classification results using a principal components reduced of the data matrix.
For example, `TS_Classify('norm','svm_linear','numPCs',10)` will compute classification when up to the top 10 PCs of the data matrix are used to classify the time-series data, yielding:

```
    Computing top 10 PCs... Done.
    Computing classification rates keeping top 1--10 PCs...
    1 PCs:   73.333 +/- 27.889%
    2 PCs:   80.000 +/- 18.257%
    3 PCs:   80.000 +/- 44.721%
    4 PCs:   73.333 +/- 14.907%
    5 PCs:   80.000 +/- 18.257%
    6 PCs:   80.000 +/- 18.257%
    7 PCs:   60.000 +/- 14.907%
    8 PCs:   53.333 +/- 18.257%
    9 PCs:   40.000 +/- 27.889%
    10 PCs:   33.333 +/- 33.333%
```
The plot shows this information graphically:

![](img/TS_Classify.png)

where the classification accuracy is shown for all features (green, dashed), and as a function of the number of leading PCs included in the classifier (black circles).

Because we have so few examples of time series in this case (5 time series from each of 3 classes), attempting to learn a classifier for the dataset using thousands of features is overkill -- we have no where near enough data to constrain such a classifier.
Indeed, these results show that a classifier using just a single feature (the first PC of the data matrix) reproduces the accuracy of a classifier the full set of 8192 features (both achieving 73.3% on this small dataset).

In small samples, it is more likely to obtain high classification accuracies by chance.
The function also allows setting the number of randomized nulls, which can be used to compute a null distribution to assess the statistical significance of the results using correctly labeled data, e.g., as `TS_Classify('norm','svm_linear','numNulls',10)`.
For example, if there are 5 examples each of two classes, a classification rate of 60% may be consistent with chance, but this same 60% classification rate would be highly significant if there are 1000 examples of each class.
