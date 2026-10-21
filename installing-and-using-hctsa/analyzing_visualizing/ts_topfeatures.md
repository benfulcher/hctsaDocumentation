# Finding informative features

In a time-series classification task, you are often interested in not only determining differences between the labeled classes, but also interpreting them in the context of your application. For example, an entropy estimate may give low values to the RR interval series measured from a healthy population, but higher values to those measured from patients with congestive heart failure. When performing a highly comparative time-series analysis, we have computed a large number of time-series features and can use these results to arrive at these types of interpretable conclusions automatically.

A simple way to determine which individual features are useful for distinguishing the labeled classes of your dataset is to compare each feature individually in terms of its ability to separate the labeled classes of time series. This can be achieved using the `TS_TopFeatures` function.

## Using `TS_TopFeatures`

The function is called as:

```
[ifeat,testStat,testStat_rand,featureClassifier,pVals,qVals] = TS_TopFeatures(whatData,whatTestStat,cfnParams,...)
```

Its inputs are:

* `whatData`: the data to analyze (default: `'norm'`, i.e., `HCTSA_N.mat`).
  The time series must have group labels assigned (using [`TS_LabelGroups`](grouping.md)), and unlabeled time series are removed.
  It can be more interpretable to run the function on filtered but unnormalized data (e.g., made using `TS_Normalize('none')`), so that feature values correspond to raw feature outputs.
* `whatTestStat`: the statistic used to score each feature individually.
  The default is `'ustat'` (the Mann–Whitney _U_ statistic, centered on its expected value) for two classes, and `'classification'` for three or more classes.
  * Two-class statistics include `'ustat'`, `'ustatP'` (−log10 of the Mann–Whitney _p_-value), `'tstat'` (Welch's _t_-statistic), `'tstatP'`, and `'svmBeta'` (the feature weights of a single linear SVM fit to all features).
    Signed statistics, like `'ustat'` and `'tstat'`, are ranked by their magnitude, with the sign showing which class has the higher values.
  * For any number of classes, `'classification'` scores each feature by the in-sample performance of a classifier (trained and tested on the same data), and `'medianClassify'` uses a simple nearest-median classifier.
* `cfnParams`: classification settings, used with `'classification'` (default: `GiveMeDefaultClassificationParams` with the `'fast-linear'` classifier, a linear discriminant).
  If `cfnParams.classifierFilename` is set, a classifier for the single best feature is saved to that file.

Optional name–value inputs:

* `'whatPlots'`: the plots to produce, from `'histogram'`, `'distributions'`, `'dendrogram'`, `'cluster'` and `'datamatrix'` (default: `{'histogram','distributions','dendrogram','cluster'}`).
* `'numTopFeatures'`: the number of top features to list and to show in the dendrogram, cluster and data matrix plots (default: 40).
* `'numFeaturesDistr'`: the number of top features to plot class distributions for (default: 16).
* `'numNulls'`: the number of null distributions to compute by shuffling the class labels (default: 0, no nulls).
  Setting it (e.g., to 10) gives _p_-values for each feature, from the pooled null statistics of all features, and FDR-corrected _q_-values (Benjamini–Hochberg).
  Computing the _q_-values uses `mafdr`, from MATLAB's Bioinformatics Toolbox.
* `'doParallel'`: whether to compute the statistics using a `parfor` loop (default: `false`).

The outputs are the ordering of features by their statistic (`ifeat`; features with a NaN statistic are omitted), the statistic for each feature (`testStat`), the null statistics (`testStat_rand`), the saved single-feature classifier (`featureClassifier`), and, when `numNulls` > 0, the _p_-values and _q_-values (`pVals`, `qVals`).

## Example: Classification of seizure from EEG

In this example, we consider 100 EEG time series from seizure, and 100 EEG time series from eyes open (from the publicly-available [Bonn University dataset](http://epileptologie-bonn.de/cms/front\_content.php?idcat=193\&lang=3)). After running [`TS_LabelGroups`](grouping.md) (as simply `TS_LabelGroups()`, which automatically detects the two keywords assigned to the two classes of signals), we run simply:

```
TS_TopFeatures()
```

(`TS_TopFeatures` analyzes `HCTSA_N.mat` by default, so the groups should be labeled before running `TS_Normalize`, which keeps the labels, or labeled in `HCTSA_N.mat` directly, as `TS_LabelGroups('norm')`.)

For these two classes, this compares each feature individually using the Mann–Whitney _U_ statistic, lists the top 40 features, and produces the default plots.
To score features instead by their in-sample linear classification accuracy, and to compare them to an empirical null distribution made by shuffling the class labels 10 times, run:

```
TS_TopFeatures('norm','classification',[],'numNulls',10)
```

The figures below were made with an older version of _hctsa_'s feature library, using classification accuracy and nulls in this way.
Feature names, IDs and counts differ in v3.0.0, but the general pattern of results is illustrative.

First, an output to screen reports the test statistic used and the number of features compared, then the mean of the statistic across all features (with the chance level, e.g., 50% accuracy for two equiprobable classes).
It then lists the top features, ordered by their test statistic, as `[ID] Name (keywords) -- statistic`.
When `numNulls` is set, it also reports how many features perform significantly better than the null distribution (at an FDR-corrected threshold of 0.05).
In this example, measures of entropy dominated the top list, including measures of the time-series distribution and Lempel–Ziv complexity.

The `'histogram'` plot summarizes the performance of the full library on the dataset, as the distribution of the test statistic across all features (with lines marking its mean and, where defined, the chance level).
When nulls are computed, the null distribution (made by shuffling the class labels randomly) is shown alongside, and the title gives the number of features that are significantly informative of the groups:

![](../../.gitbook/assets/TS\_TopFeatures\_histograms.png)

Here we can see that the features (blue) are performing much better than the randomized features (null: red).

Next, the `'distributions'` plots show the class distributions of the top features (`numFeaturesDistr`, 16 by default, with up to 16 per figure), with the test statistic of each feature in its title. For example:

![](../../.gitbook/assets/TS\_TopFeatures\_distributions\_.png)

This allows us to interpret the values of features in terms of the dataset. After some inspection of the listed operations, we find that various measures of EEG Sample Entropy are higher for healthy individuals with eyes open (blue) than for seizure signals (purple).

Finally, we can visualize how the top features depend on each other, to try to deduce the main independent behaviors in the set of top features (`numTopFeatures`, 40 by default; 25 in the example below) for the given classification task.
Dependencies are measured as absolute Spearman correlations, |ρ|, between all pairs of top features.
The `'dendrogram'` plot shows an average-linkage dendrogram of the top features under the distance 1 − |ρ|, labeled with each feature's ID, name, keywords and statistic.
The `'cluster'` plot shows the matrix of |ρ| between all pairs of top features, reordered by clustering (with the dendrogram to the right of the plot):

![](../../.gitbook/assets/TS\_TopFeatures\_cluster.png)

The features are split into clusters at a threshold of 1 − |ρ| = 0.25, and the number of clusters is given in the title (5 in the example shown).
Each cluster is outlined by a box, and a smaller box marks a representative feature of each cluster (the member closest to the others in the cluster).
(The box colors and correlation measure in the figure above, made with an older version, differ from those of v3.0.0.)

Here we see that most pairs of features are quite dependent (at |_R_| > ~0.8), and the main clusters are those measuring distribution entropy (`EN_DistributionEntropy_`), Lempel-Ziv complexity (`EN_LZComplexity_`), Sample Entropy (`EN_SampEn_`), and a one-step-ahead surprise metric (`FC_Surprise_`).

The optional `'datamatrix'` plot shows the data matrix of the top features, with time series colored and ordered by group.

This function produces all of the basic visualizations required to achieve a basic understanding of the differences between the labeled classes of the dataset, and should be considered a first step in a more detailed analysis. For example, in this case we may investigate the top-performing features in more detail, to understand in detail what they are measuring, and how. This process is described in the [Interpreting features](interpreting-features.md) section.
