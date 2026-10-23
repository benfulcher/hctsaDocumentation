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

In this example, we consider 100 EEG time series from seizure, and 100 EEG time series from eyes open (from the publicly-available [Bonn University dataset](http://epileptologie-bonn.de/cms/front\_content.php?idcat=193\&lang=3)).
The two classes have the keywords `eyesOpen` and `seizure`, which we use to label them with [`TS_LabelGroups`](grouping.md) (running `TS_LabelGroups()` with no keywords detects these two keywords automatically, and asks you to confirm them).
We label the groups before running `TS_Normalize` (which keeps the labels), because `TS_TopFeatures` analyzes `HCTSA_N.mat` by default:

```
TS_LabelGroups('raw',{'eyesOpen','seizure'});
TS_Normalize();
```

(Alternatively, you can label groups in `HCTSA_N.mat` directly, as `TS_LabelGroups('norm',...)`.)
Then we run simply:

```
TS_TopFeatures()
```

For these two classes, this compares each feature individually using the Mann–Whitney _U_ statistic, lists the top 40 features, and produces the default plots.
The output to screen reports the classes, the test statistic used and the number of features compared, then the mean of the statistic across all features (with its chance level, 0 for the centered _U_ statistic), and lists the top features, ordered by their test statistic, as `[ID] Name (keywords) -- statistic` (here trimmed with `...`):

```
>> TS_TopFeatures()
Loading data from HCTSA_N.mat... Done.
2 classes assigned to the time series in this dataset:
Class 1: eyesOpen (100 time series)
Class 2: seizure (100 time series)
Using 'ustat' test statistic by default.
Computing the performance of 6126 individual features to differentiate 2 classes using a Mann-Whitney U (centered)...
 Done in 1.1s.
Mean Mann-Whitney U (centered) across 6126 features = 213.50
(Random guessing for 2 equiprobable classes = 0.00)
[603] CO_JointNonGaussianity_ac1e_3_ac1_10000_mardiaSkew (correlation,nonlinear,distribution) -- -5000.00
[974] DN_CompareKSFit_uni_psy (distribution,ksdensity,raw,spreadDependent) -- -5000.00
[1025] DN_Moments_raw_4 (distribution,moment,raw,spreadDependent) -- -5000.00
...
[1263] standard_deviation (distribution,spread,raw,spreadDependent) -- -5000.00
[1367] EN_MSE_1-10_2_015_diff1_sampen_s2 (entropy) -- 5000.00
[1579] FC_LocalSimple_lfit2_normr2 (model,forecasting) -- 5000.00
...
```

The 6126 features are those that remain after `TS_Normalize` filtering (of the 6392 in the v3.0.0 library).
The largest possible magnitude of the centered _U_ statistic is 100 × 100/2 = 5000, so the top 20 features listed separate the two classes perfectly.
The sign gives the direction of the difference: negative values (e.g., `standard_deviation`) are lower for the first class, `eyesOpen`, than for `seizure`, and positive values (e.g., `EN_MSE_1-10_2_015_diff1_sampen_s2`) are higher.

To score features instead by their in-sample linear classification accuracy, and to compare them to an empirical null distribution made by shuffling the class labels 10 times, run:

```
TS_TopFeatures('norm','classification',[],'numNulls',10)
```

The null distribution is made by random shuffles, so set the random seed first (e.g., `rng(0)`, which we used) to make it reproducible.
The figures below come from this run.
When `numNulls` is set, the output also reports how many features perform significantly better than the null distribution (at an FDR-corrected threshold of 0.05).
Here is its output to screen (trimmed, and without the progress bars):

```
>> rng(0)
>> TS_TopFeatures('norm','classification',[],'numNulls',10)
Loading data from HCTSA_N.mat... Done.
2 classes assigned to the time series in this dataset:
Class 1: eyesOpen (100 time series)
Class 2: seizure (100 time series)
Computing the performance of 6126 individual features to differentiate 2 classes using a simple linear classifier accuracy...
 Done in 2.1s.
Mean simple linear classifier accuracy across 6126 features = 69.98%
(Random guessing for 2 equiprobable classes = 50.00%)
[974] DN_CompareKSFit_uni_psy (distribution,ksdensity,raw,spreadDependent) -- 99.50%
[1342] EN_LZComplexity_7_diff (entropy,mex) -- 99.50%
[1344] EN_LZComplexity_8_diff (entropy,mex) -- 99.50%
[1346] EN_LZComplexity_9_diff (entropy,mex) -- 99.50%
[3499] NL_TakensEstimator_n1_c001_ac1_1_3 (nonlinear,scaling,dimension,tisean) -- 99.50%
[3529] NL_d2_1_10_ac1_takens05_iqr (nonlinear,tisean,dimension) -- 99.50%
[4193] PP_SchreiberDenoise_5_1_1_03_ac1Change (preprocessing,nonlinear,correlation) -- 99.50%
[4200] PP_SchreiberDenoise_5_1_4_03_ac1Change (preprocessing,nonlinear,correlation) -- 99.50%
[359] CO_Embed2_AngleTau_50_max_thetaac1 (correlation,embedding) -- 99.00%
[1340] EN_LZComplexity_6_diff (entropy,mex) -- 99.00%
...
[1455] EN_SampEn_5_02_sampen3 (entropy) -- 97.50%
Now for 10 nulls... 1,2,3,4,5,6,7,8,9,10.
61260 simple linear classifier accuracy statistics computed in 10.1s.
Estimating FDR-corrected p-values across all features by pooling across 10 nulls.
(Given strong dependences across 6126 features, will produce conservative p-values)
4694/6126 features show better performance using simple linear classifier accuracy than the null distribution
at the magical 0.05 threshold (FDR corrected).
...
abscorr distance-based clustering with 2 clusters.
```

In this example, the top list is dominated by measures of entropy and complexity (Lempel–Ziv complexity, `EN_LZComplexity_`, and sample, permutation and fuzzy entropies), estimates of the correlation dimension (`NL_TakensEstimator_`, `NL_d2_`, `NL_c1_`), and the frequencies of symbolic motifs (`SB_MotifThree_`).

The `'histogram'` plot summarizes the performance of the full library on the dataset, as the distribution of the test statistic across all features (with lines marking its mean and, where defined, the chance level).
When nulls are computed, the null distribution (made by shuffling the class labels randomly) is shown alongside, and the title gives the number of features that are significantly informative of the groups:

![](../../.gitbook/assets/TS\_TopFeatures\_histograms.png)

Here we can see that the features (real: blue, with a mean accuracy of 69.98%) are performing much better than the randomized features (null: red, close to the 50% chance level), with 4694 features significantly informative of the groups.

Next, the `'distributions'` plots show the class distributions of the top features (`numFeaturesDistr`, 16 by default, with up to 16 per figure), with the test statistic of each feature in its title. For example:

![](../../.gitbook/assets/TS\_TopFeatures\_distributions\_.png)

This allows us to interpret the values of features in terms of the dataset.
Here, `eyesOpen` is red and `seizure` is blue (the values are the normalized feature values in `HCTSA_N.mat`).
After some inspection of the listed operations, we find that Lempel–Ziv complexity, sample entropy and correlation-dimension estimates are higher for healthy individuals with eyes open (red) than for seizure signals (blue).
The two `raw`, `spreadDependent` features among them (`DN_CompareKSFit_uni_psy` and `MD_RawHRVMeas_CVI`), which depend on the amplitude of the EEG signal, are instead higher for seizure.

Finally, we can visualize how the top features depend on each other, to try to deduce the main independent behaviors in the set of top features (`numTopFeatures`, 40 by default, as in this example) for the given classification task.
Dependencies are measured as absolute Spearman correlations, |ρ|, between all pairs of top features.
The `'dendrogram'` plot shows an average-linkage dendrogram of the top features under the distance 1 − |ρ|, labeled with each feature's ID, name, keywords and statistic, with branches below 1 − |ρ| = 0.25 colored by cluster:

![](../../.gitbook/assets/TS\_TopFeatures\_dendrogram.png)

The `'cluster'` plot shows the matrix of |ρ| between all pairs of top features, reordered by clustering (with the dendrogram to the right of the plot):

![](../../.gitbook/assets/TS\_TopFeatures\_cluster.png)

The features are split into clusters at a threshold of 1 − |ρ| = 0.25, and the number of clusters is given in the title (2 in this example).
Each cluster is outlined by a black box, and a smaller gray box marks a representative feature of each cluster (the member closest to the others in the cluster).

Here we see that the top features are all strongly dependent: |ρ| > 0.67 for every pair, and |ρ| > 0.7 for 98.8% of pairs.
The two clusters are a pair of amplitude-dependent distributional features (`DN_CompareKSFit_uni_psy` and `MD_RawHRVMeas_CVI`, with |ρ| = 0.95), and a large cluster of the other 38 features, measuring entropy, complexity and correlation dimension (represented by `NL_TakensEstimator_n1_c001_ac1_1_3`).

The optional `'datamatrix'` plot shows the data matrix of the top features, with time series colored and ordered by group.

This function produces all of the basic visualizations required to achieve a basic understanding of the differences between the labeled classes of the dataset, and should be considered a first step in a more detailed analysis. For example, in this case we may investigate the top-performing features in more detail, to understand in detail what they are measuring, and how. This process is described in the [Interpreting features](interpreting-features.md) section.
