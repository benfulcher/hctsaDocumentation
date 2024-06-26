# Finding informative features

In a time-series classification task, you are often interested in not only determining differences between the labeled classes, but also interpreting them in the context of your application. For example, an entropy estimate may give low values to the RR interval series measured from a healthy population, but higher values to those measured from patients with congestive heart failure. When performing a highly comparative time-series analysis, we have computed a large number of time-series features and can use these results to arrive at these types of interpretable conclusions automatically.

A simple way to determine which individual features are useful for distinguishing the labeled classes of your dataset is to compare each feature individually in terms of its ability to separate the labeled classes of time series. This can be achieved using the `TS_TopFeatures` function.

## Example: Classification of seizure from EEG

In this example, we consider 100 EEG time series from seizure, and 100 EEG time series from eyes open (from the publicly-available [Bonn University dataset](http://epileptologie-bonn.de/cms/front\_content.php?idcat=193\&lang=3)). After running [`TS_LabelGroups`](grouping.md) (as simply `TS_LabelGroups()`, which automatically detects the two keywords assigned to the two classes of signals), we run simply:

```
TS_TopFeatures()
```

By default this function will compare the in-sample linear classification performance of all operations in separating the labeled classes of the dataset (individually), produce plots summarizing the performance across the full library (compared to an empirical null distribution), characterize the top-performing features, and show their dependencies on the dataset. Inputs to the function control how these tasks are performed (including the statistic used to measure individual performance, what plots to produce, and whether to produce nulls).

First, we get an output to screen, showing the mean linear classification accuracy across all operations, and a list of the operations with top performance (ordered by their test statistic, with their ID shown in square brackets, and keywords in parentheses):

```
Mean linear classifier across 8261 operations = 67.02%
(Random guessing for 2 equiprobable classes = 50.00%)
[908] EN_DistributionEntropy_raw_ks__005 (entropy,raw,spreaddep) -- 100.00%
[982] DN_FitKernelSmoothraw_max (distribution,ksdensity,raw,spreaddep) -- 100.00%
[902] EN_DistributionEntropy_raw_ks_01_0 (entropy,raw,spreaddep) -- 99.50%
[903] EN_DistributionEntropy_raw_ks_02_0 (entropy,raw,spreaddep) -- 99.50%
[904] EN_DistributionEntropy_raw_ks_05_0 (entropy,raw,spreaddep) -- 99.50%
[905] EN_DistributionEntropy_raw_ks_1_0 (entropy,raw,spreaddep) -- 99.50%
[906] EN_DistributionEntropy_raw_ks__001 (entropy,raw,spreaddep) -- 99.50%
[907] EN_DistributionEntropy_raw_ks__002 (entropy,raw,spreaddep) -- 99.50%
[909] EN_DistributionEntropy_raw_ks__01 (entropy,raw,spreaddep) -- 99.50%
[1127] EN_MS_LZcomplexity_8_diff (MichaelSmall,complexity,mex,LempelZiv) -- 99.50%
[1128] EN_MS_LZcomplexity_9_diff (MichaelSmall,complexity,mex,LempelZiv) -- 99.50%
[3412] DN_CompareKSFit_uni_psy (distribution,ksdensity,uni,peaksepy,raw,locdep) -- 99.50%
[901] EN_DistributionEntropy_raw_ks_005_0 (entropy,raw,spreaddep) -- 99.00%
[1129] EN_MS_LZcomplexity_10_diff (MichaelSmall,complexity,mex,LempelZiv) -- 99.00%
[2958] EN_SampEn_5_01_diff1_sampen4 (entropy,sampen,controlen) -- 99.00%
[910] EN_DistributionEntropy_raw_ks__02 (entropy,raw,spreaddep) -- 98.50%
[980] DN_FitKernelSmoothraw_entropy (distribution,ksdensity,entropy,raw,spreaddep) -- 98.50%
[2303] SY_SpreadRandomLocal_ac2_100_meansampen1_015 (stationarity) -- 98.50%
[2616] FC_Surprise_T1_100_4_udq_500_lq (information,symbolic) -- 98.50%
[2624] FC_Surprise_T1_100_5_udq_500_lq (information,symbolic) -- 98.50%
```

Here we see that measures of entropy are dominating this top list, including measures of the time-series distribution.

An accompanying plot summarizes the performance of the full library on the dataset, compared to a set of nulls (generated by shuffling the class labels randomly):

![](../../.gitbook/assets/TS\_TopFeatures\_histograms.png)

Here we can see that the default feature library (blue: 7275 features remaining after filtering and normalizing) are performing much better than the randomized features (null: red).

Next we get a set of plots showing the class probability distributions for the top features. For example:

![](../../.gitbook/assets/TS\_TopFeatures\_distributions\_.png)

This allows us to interpret the values of features in terms of the dataset. After some inspection of the listed operations, we find that various measures of EEG Sample Entropy are higher for healthy individuals with eyes open (blue) than for seizure signals (purple).

Finally, we can visualize how the top operations depend on each other, to try to deduce the main independent behaviors in this set of 25 top features for the given classification task:

![](../../.gitbook/assets/TS\_TopFeatures\_cluster.png)

In this plot, the magnitude of the linear correlation, `R` (as `1-|R|`) between all pairs of top operations is shown using color, and a visualization of a clustering into 5 groups is shown (with green boxes corresponding to clusters of operations based on the dendrogram shown to the right of the plot). Blue boxes indicate a representative operation of each cluster (closest to the cluster centre).

Here we see that most pairs of operations are quite dependent (at `|R| > ~0.8`), and the main clusters are those measuring distribution entropy (`EN_DistributionEntropy_`), Lempel-Ziv complexity (`EN_MS_LZcomplexity_`), Sample Entropy (`EN_SampEn_`), and a one-step-ahead surprise metric (`FC_Surprise_`).

This function produces all of the basic visualizations required to achieve a basic understanding of the differences between the labeled classes of the dataset, and should be considered a first step in a more detailed analysis. For example, in this case we may investigate the top-performing features in more detail, to understand in detail what they are measuring, and how. This process is described in the [Interpreting features](https://github.com/hctsa-users/hctsa-manual/tree/c20c1212f3966ea038e80867ea0eac7f605cb93e/interpreting\_features.md) section.
