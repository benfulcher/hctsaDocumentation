# Exploring classification accuracy

When performing a time-series classification task, a basic first exploration of the data is to investigate how accurately a classifier can learn a mapping from time-series features to labels assigned to time series in your dataset.

The first step is to assign group labels to time series in your dataset using [`TS_LabelGroups`](grouping.md).

Depending on the classifier, you typically want to first normalize the features to put them all on a similar scale (using `TS_Normalize`).

Depending on the question asked of the data, you should also consider whether certain types of features should be removed. For example, you may wish to exclude length-dependent features (if differences in time-series length vary between classes but are an uninteresting artefact of the measurement). This can be done using `TS_Subset` (and functions like `TS_CompareFeatureSets` described below allow you to test the sensitivity of these results).

## Classifying labeled groups (`TS_Classify`)

`TS_Classify` uses all of the features in a given _hctsa_ data matrix to classify assigned class labels.

### Setting properties of the classification

You can set classification settings, from the number of folds to use in cross-validation to the type of classifier, as the `cfnParams` structure. For the labeling defined in a given `TimeSeries` table, you can set defaults for this using `cfnParams = GiveMeDefaultClassificationParams('norm')` (takes `TimeSeries` labeling from `HCTSA_N.mat`). This automatically sets an appropriate number of folds (for cross-validation), and includes settings for taking into account class imbalance in classifier training and evaluation. It is best to alter the values inside this function to suit your needs, such that these settings can be applied consistently.

### Computing classification accuracy

First let's run a simple classification of the groups labeled in `HCTSA_N.mat`, using default classification settings:

```
TS_Classify('norm')
```

In large feature spaces like in _hctsa_, simpler classifiers (like a linear SVM, the default `'svm-linear-lowdim'`, or `'svm-linear'`) tend to generalize well, but you can play with the settings in `cfnParams` to get a sense for how the performance varies.
The classifier is set by `cfnParams.whatClassifier`; the options are `'svm-linear'`, `'svm-linear-lowdim'`, `'svm-rbf'`, `'linear'`, `'fast-linear'`, `'knn'` and `'logistic'`.

As well as the classification results, the function also produces a confusion matrix, which is especially useful for evaluating where classification errors are occurring.
Here's an example for a five-class problem: the 500 time series of the Bonn EEG dataset, labeled using `TS_LabelGroups('raw',{'seizure','eyesOpen','eyesClosed','epileptogenic','hippocampus'})` and normalized using `TS_Normalize`.
Running `TS_Classify('norm')` reports the mean (and standard deviation) of the accuracy across the ten test folds:

```
Mean +/- std (across 10 folds) accuracy (5-class) using svm-linear-lowdim classification with 6119 features:
92.400 +/- 3.098%
[Training folds: mean = 100.000%, std = 0.000% across 10 folds].
```

and plots the confusion matrix (rows are the true classes, columns the predicted classes):

![](../../.gitbook/assets/TS\_Classify\_ConfusionMatrix.png)

Most errors here are between the two classes of intracranial recordings (10 `epileptogenic` time series are predicted as `hippocampus`, and 12 `hippocampus` time series as `epileptogenic`), and 6 `eyesClosed` time series are predicted as `eyesOpen`.

### Normalizing within cross-validation folds

By default, the classifier is cross-validated on the data as given.
For data normalized by `TS_Normalize`, the feature filtering and the normalization of each feature were fitted on all time series, including those in each test fold, so cross-validated accuracies can be slightly optimistic.
To avoid this, set `'normalizeWithinFolds'` to fit the filter and normalization on each training fold only (the unnormalized data, `HCTSA.mat` here, are loaded for this):

```
TS_Classify('norm',cfnParams,0,'normalizeWithinFolds',true)
```

Other optional inputs to `TS_Classify` include `'doPlot'` (whether to plot outputs) and `'seedReset'` (for reproducible cross-validation).
Setting `cfnParams.classifierFilename` trains a classifier on all the data and saves it to that file, for use by `TS_Predict`.

### Assessing significance as a permutation test relative to a shuffled ensemble

In datasets containing fewer time series, it is more likely to obtain high classification accuracies by chance. You may therefore wonder how confident you can be with your classification accuracy. For example if you get a two-class classification accuracy of 60%, you might wonder what the probability is of obtaining such an accuracy by chance?

You can set `numNulls` in `TS_Classify` to build up a null distribution of accuracies from shuffled class labels, from which you can estimate a _p_-value to infer the significance of the classification accuracy obtained with the true data labeling provided.
By default (`'simpleNull'` = `true`), a fast null is used, which scores shuffled labels as predictions in each test fold (no classifier is trained).
Setting `'simpleNull'` to `false` gives a model-based null, which repeats the classification with the settings defined in `cfnParams` on shuffled class labels (this is slower, as the classifier is retrained for every null).
Model-based nulls can be run across multiple cores by switching on `'doParallel'` (this requires `cfnParams.computePerFold = false`).

For example, for a two-class problem from the Bonn EEG dataset (the 100 `epileptogenic` and 100 `hippocampus` time series, kept using `TS_FilterData`, labeled using `TS_LabelGroups('raw',{'epileptogenic','hippocampus'})`, and normalized), we computed 100 model-based nulls:

```
numNulls = 100;
dataFile = 'HCTSA_N.mat';
cfnParams = GiveMeDefaultClassificationParams(dataFile);
TS_Classify(dataFile,cfnParams,numNulls,'simpleNull',false)
```

This gives you a _p_-value estimate (both via a direct permutation test, and by assuming a Gaussian null distribution):

```
Mean +/- std (across 10 folds) accuracy (2-class) using svm-linear-lowdim classification with 6077 features:
89.500 +/- 7.619%
...
Mean accuracy (2-class) using 10-fold linear SVM classifier (low-dimensional) classification with 6077 features across 100 nulls:
49.870 +/- 4.149%

Estimated p-value (permutation test) = 0
Estimated p-value (Gaussian fit) = 0
```

A permutation-test _p_-value of 0 means that none of the 100 nulls reached the real accuracy (i.e., _p_ < 0.01; more nulls are needed to resolve smaller _p_-values).
The function also plots the null distribution (gray) with the true result annotated (red: the cross-validated accuracy, solid, and the accuracy on the training folds, dashed):

![](../../.gitbook/assets/TS\_Classify\_NullDistribution.png)

## Comparing feature sets

### Specific feature sets (`TS_CompareFeatureSets`)

You might wonder whether the classification results are driven by simple types of features that aren't related to time-series dynamics at all (such as the mean of the data, or time-series length).

These can be filtered out from the initial computation (e.g., when performing `TS_Init`), or subsequently (e.g., using `TS_Subset`), but you can test the effect such features are having on your dataset using `TS_CompareFeatureSets`.
By default, `TS_CompareFeatureSets('norm')` compares the sets `'all'`, `'catch22'`, `'linearAutoCorrs'`, `'quantiles'`, `'linearAutoCorrsAndQuantiles'`, `'notLocationDependent'`, `'locationDependent'`, `'notLengthDependent'`, `'lengthDependent'`, `'notSpreadDependent'` and `'spreadDependent'` (a custom list can be given as the third input).
The `'catch22'` set is skipped, with a warning, unless the dataset contains the _catch22_ features (computed using `TS_Init(...,'catch22')`).
Here's an example output, from `TS_CompareFeatureSets('norm')` for the two-class problem above (`epileptogenic` versus `hippocampus`, for which chance accuracy is 50%), where _catch22_ was not computed:

![](../../.gitbook/assets/TS\_CompareFeatureSets.png)

Each violin shows the accuracy in each of the ten test folds (the number of features in each set is given in parentheses), and the mean accuracy of each set is also printed to screen.
All 6077 features give a mean accuracy of 89.5%, while small sets of simple features perform much worse: 59.0% for the 20 linear autocorrelations, 53.5% for the 9 quantiles, and 60.5% for both together.
The 16 location-dependent (45.0%) and 34 spread-dependent (53.5%) features are close to chance, and excluding them (90.5% and 91.0%) does not reduce the accuracy, so differences in the mean or spread of the recordings are not driving the classification.
All of these recordings have the same length (4097 samples), so the `length` feature was removed by `TS_Normalize` (it is constant).
The two remaining features with the `lengthDependent` keyword, `NW_VisibilityGraph_horiz_full_evnlogL` and `NW_VisibilityGraph_norm_20000_evnlogL`, also depend on the dynamics of the time series and give 62.5%, but excluding them (`'notLengthDependent'`, 90.0%) does not alter the classification accuracy.
In datasets where time-series length differs between classes but is an uninteresting artefact of the measurement, it would be advisable to remove length-dependent features for peace of mind.

## Comparing to lower-dimensional feature spaces

The complexity of the time-series analysis literature is necessary for strong classification results to different degrees, depending on the task.
You can quickly assess how accurately a smaller number of reduced components (e.g., Principal Components) can classify your dataset using `TS_ClassifyLowDim`.
For the two-class problem above, `TS_ClassifyLowDim('norm')` (which uses up to 10 PCs by default) gives:

![](../../.gitbook/assets/TS\_Classify\_LowDim.png)

The left panel shows the cross-validated classification accuracy as a function of the number of leading PCs included in the classifier (black circles, with gray lines at ± one standard deviation across folds), and the accuracy on the training folds (orange circles).
Note that this is cumulative: '5 PCs' means classification in the five-dimensional space of the five leading PCs.
Horizontal lines show the cross-validated accuracy using all 6077 features (pink, 90.0 ± 7.5%; this differs slightly from the 89.5% above as the cross-validation folds are drawn differently), its training-fold accuracy (green, dashed, 100%), and the mean and 95% quantile of a naive null (gray, from 1000 label shuffles).
By default, `TS_ClassifyLowDim` also keeps adding PCs until the classifier reaches 100% accuracy on the training folds (38 PCs here), and shows the cross-validated accuracy at that point (red, 85.0 ± 7.1%).
The two panels on the right compare the fold-average and individual-fold accuracies of the naive null, all features, and this number of PCs.

The accuracy for each number of PCs is also printed to screen (training-fold accuracy in square brackets):

```
Computing accuracy, keeping top 1-10 PCs...
1 PCs: [in-fold: 51.6%] 49.0 +/- 10.5%
2 PCs: [in-fold: 63.3%] 62.5 +/- 10.9%
3 PCs: [in-fold: 71.9%] 71.0 +/- 11.0%
4 PCs: [in-fold: 79.6%] 79.0 +/- 5.2%
5 PCs: [in-fold: 80.1%] 78.0 +/- 11.6%
...
10 PCs: [in-fold: 80.3%] 75.0 +/- 10.0%
Let's keep searching for perfect in-sample accuracy...
11 PCs: [81.3%] 77.5 +/- 5.4%
...
38 PCs: [100.0%] 85.0 +/- 7.1%
Now with all 6077 features for comparison...
```

Here we find that we can get decent classification accuracy (79.0%) with just four PCs, with little improvement from adding up to ten PCs, while all 6077 features give 90.0% (and perhaps even more complex classifiers will give better results in the lower-dimensional spaces).

You can quickly interpret the type of features loading strongly onto each PC from the information shown to screen, which lists the PC coefficient and the correlation, `r`, of each top feature with the PC, followed by its ID, name and keywords (the top 20 features are listed for each PC).
For example:

```
---Top feature loadings for PC1---:
(0.027, r = 0.99) [1449] EN_SampEn_5_01_diff1_sampen0 (entropy)
(0.027, r = 0.99) [132] ApEn1_01 (entropy)
(0.027, r = 0.99) [1453] EN_SampEn_5_02_sampen1 (entropy)
(0.027, r = 0.98) [1478] EN_SampEn_5_015_sampen1 (entropy)
(0.027, r = 0.98) [1468] EN_SampEn_5_03_sampen1 (entropy)
...

---Top feature loadings for PC2---:
(0.036, r = 0.91) [2664] MF_steps_ahead_ar_2_6_meanabs_h4 (model,prediction)
(0.036, r = 0.90) [2667] MF_steps_ahead_ar_2_6_meanabs_h5 (model,prediction)
(-0.035, r = -0.90) [5164] SC_FluctAnal_2_dfa_50_2_logi_stdssr (scaling)
(-0.035, r = -0.89) [5177] SC_FluctAnal_2_dfa_50_2_logi_alphadiff (scaling)
(-0.035, r = -0.89) [5180] SC_FluctAnal_2_dfa_50_3_logi_ssr (scaling)
...
```

This demonstrates that, on this dataset, entropy measures (sample entropy and approximate entropy) are the features most strongly correlated to PC1, and the errors of multistep-ahead AR-model predictions and detrended fluctuation analysis statistics are most strongly correlated to PC2.
