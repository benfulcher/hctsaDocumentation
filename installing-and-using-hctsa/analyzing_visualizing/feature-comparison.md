# Comparing to existing features

One of the key goals of highly comparative time-series analysis, is to allow unbiased methodological comparison between the vast literature of time-series analysis tools developed for different applications. By representing features in terms of their outputs across a time-series dataset, the context of a given feature can be assessed by searching the database for features with similar behavior. The search can be done using a diverse range of real and model-generated data, or using a more specific dataset if this is more appropriate for a given application (e.g., looking just at EEG signals). Just like [similar time series to a target can be retrieved and visualized](sim\_search.md), similar features to a given target feature can also be retrieved using `TS_SimSearch`.

This chapter will give instructions on how you can compare a new time-series analysis feature to our library of over 6000 time-series features (6392 in _hctsa_ v3.0.0) using _hctsa_.
We assume that the reader has [installed _hctsa_](../setup/), which will be required to work with files and compute features.

## Setting a data context

The first step is defining the set of features to compare to (here we use the default _hctsa_ library), and the set of time-series data that behavior is going to be assessed on. If you have just developed a new algorithm for time-series analysis and want to see how it performs across a range of interdisciplinary time-series data, then you may want to use a diverse set of time series sampled from across science. This can be easily achieved using our set of 1000 time series, a random selection of 25 such time series are plotted below (only the first 250 samples are plotted to aid visualization):

![](../../.gitbook/assets/Empirical1000\_25\_250samples.png)

The time series, an _hctsa_ input file (`INP_1000ts.mat`) and a pre-computed feature matrix (`HCTSA_Empirical1000.mat`) can be downloaded from [figshare](https://doi.org/10.6084/m9.figshare.5436136).
The pre-computed matrix there was computed with a pre-release version of _hctsa_ v3 (v3.0.0-alpha), so its feature names and values differ from those of v3.0.0, and results from different versions should not be mixed.
We therefore recommend recomputing the features with your version of _hctsa_, which also ensures they match your local compute architecture: initialize with `TS_Init('INP_1000ts.mat');`, then compute using [`TS_Compute`](../calculating/running\_computations.md) (e.g., `TS_Compute('series')`, which parallelizes the computation over time series).

Another diverse data context is the [1000 × 1000 collection](https://dynamicsandneuralsystems.github.io/1000x1000/): 1000 simulated time series, each 1000 samples long, from 133 generating processes across 13 classes of dynamics (including noise and linear processes, maps, chaotic flows, oscillators, point processes and models of natural systems).
Each series comes with its generating process, parameters and property tags, so you can relate a feature's behavior to known dynamical properties.
The data, including an _hctsa_ input file (`INP_1000x1000.mat`), are on [figshare](https://doi.org/10.6084/m9.figshare.34013331), and the interactive website lets you browse, listen to and map the collection.
As for Empirical1000, its pre-computed feature matrix predates v3.0.0, so recompute the features from the input file.

However, if you only ever analyze a particular type of data (e.g., rainfall), then perhaps you're more interested in which methods perform similarly on rainfall data. For this case, you can produce your own data context for custom data using properly structured input files [as explained here](../calculating/input\_files.md).

## _EXAMPLE 1_: Determining the relationship between a new feature and existing features

We use the (hypothetical) example of a hot new feature, :boom: `hot_feature1` :boom:, recently published in _Science_ (and not yet in the _hctsa_ library), and attempt to determine whether it is completely new, or whether there are existing features that exhibit similar performance to it. Think first about the data context (described above), which allows you to understand the behavior of thousands of features on a diverse dataset with which to compare the behavior of our new feature, `hot_feature1`. This example uses the `Empirical1000` data context, `HCTSA_Empirical1000.mat`, described above.

### 1. Computing the new features

Getting the feature values for the new feature, `hot_feature1`, could be done directly (using `TS_CalculateFeatureVector`), but in order to maintain the HCTSA structure, we instead produce a new `HCTSA.mat` file containing just `hot_feature1` and the same time series.
To enable a valid comparison, the library features in your data context (e.g., `HCTSA_Empirical1000.mat`) should all have been computed with the same version of _hctsa_.

We first generate an input file, `INP_hot_master.txt` containing the function call, that takes in a time series, `x`:

```
MyHotFeature(x)     hot_master
```

Any additional arguments to the function `MyHotFeature.m` should be specified here. `MyHotFeature.m` must also be in a form that outputs a structure (or a single real number, [as explained here](../calculating/input\_files.md)).

The interesting field in the structure output produced by `MyHotFeature(x)` is `hotFeature1`, which needs to be specified in another input text file, `INP_hot_features.txt`, for example, as:

```
hot_master.hotFeature1     hot_feature1      hot,science
```

where we have given this feature two keywords: `hot` and `science`.

So now we are able to initiate a new _hctsa_ calculation, specifying custom code calls (_master_) and features to extract from the code call (_features_), as:

```
TS_Init('INP_1000ts.mat',{'INP_hot_master.txt','INP_hot_features.txt'},true,'HCTSA_hot.mat');
```

This generates a new file, `HCTSA_hot.mat`, containing information about the 1000 time series, and the new hot feature, `hot_feature1`, which can then be computed as:

```
TS_Compute('none',[],[],'missing','HCTSA_hot.mat');
```

### 2. Combining

So now we have both a context of the behavior of a library of >6000 features on 1000 diverse time series, and we also have the behavior of our hot new feature.
It is time to combine them and look for inter-relationships!

```
TS_Combine('HCTSA_Empirical1000.mat','HCTSA_hot.mat',true,true,'HCTSA_merged.mat');
```

Here the fourth input (`true`) merges the two different sets of features.
This requires the two files to contain the same time series, in the same order (as here, where both were initialized from the same input file).

### 3. Comparing

Now that we have all of the data in the same HCTSA file, we can compare the behavior of the new feature to the existing library of features.
This can be done manually by the researcher, or by using standard _hctsa_ functions; the most relevant is [`TS_SimSearch`](sim\_search.md).
We can find the ID assigned to our new `hot_feature1` in the merged HCTSA file as:

```
load('HCTSA_merged.mat','Operations');
hotID = Operations.ID(strcmp(Operations.Name,'hot_feature1'))
```

(`TS_Combine` re-indexes the features of the merged file, so the new feature's ID follows those of the library features.)
Then we can use [`TS_SimSearch`](sim\_search.md) to explore the relationship of our hot new feature to other features in the _hctsa_ library:

```
TS_SimSearch('targetID',hotID,'tsOrOps','ops','whatData','HCTSA_merged.mat','whatPlots',{'scatter','matrix'})
```

For features, `TS_SimSearch` measures distances as 1 − _r_, for Pearson correlation coefficients, _r_, by default (`'corr_fast'`), so features that are strongly anticorrelated with the target are not counted as matches.
To find features that are strongly correlated with the target in either direction, set `'whatDistMetric','abscorr'` (distances 1 − |ρ|, for Spearman correlation coefficients, ρ).
(If the data contain distances stored by `TS_Cluster`, those are used instead.)

`TS_SimSearch` prints the closest matches (20 by default; set `'numNeighbors'`) to screen, each with its ID, name, keywords and correlation to the target.
The `'scatter'` plot shows scatter plots of the target feature against each of its closest matches (up to 12), and you can click on a plot to switch between values and ranks.

In the example below, made with an older version of _hctsa_, our feature reproduced the behavior of the first zero-crossing of the autocorrelation function (in _hctsa_ v3.0.0 this is the feature `firstZero_acf_tau`; see [Interpreting Features](interpreting-features.md) for more info on how to interpret matching features):

![](<../../.gitbook/assets/Screen Shot 2017-09-25 at 18.43.11.png>)

The `'matrix'` plot shows the pairwise distances between the target and its matches, reordered by clustering, and provides another visualization of the context of this hot new feature (in this case there are so many highly correlated features, that the matrix doesn't reveal much subtle structure):

![](<../../.gitbook/assets/Screen Shot 2017-09-25 at 18.42.09.png>)

### 4. Interpreting

In this case, the hot new feature wasn't so hot: it was highly (linearly) correlated to many existing features (including the simple zero-crossing of the autocorrelation function, `firstZero_acf_tau`), even across a highly diverse time-series dataset. However, if you have more luck and come up with a hot new feature that shows distinctive (and useful) performance, then it can be incorporated in the default set of features used by _hctsa_ by adding the necessary master and feature definitions (i.e., the text in `INP_hot_master.txt` and the text in `INP_hot_features.txt`) to the library files (`INP_mops_hctsa.txt` and `INP_ops_hctsa.txt` in the **FeatureSets** directory of _hctsa_), as explained [here](../calculating/input_files.md). You might even celebrate your success by sharing your new feature with the community, by sending a [Pull Request](https://help.github.com/articles/using-pull-requests/) to the [hctsa github repository](https://github.com/benfulcher/hctsa)!! :satisfied:

## _EXAMPLE 2_: Determining the relationship between an existing _hctsa_ feature and the rest of the library.

If using a set of 1000 time series, then this is easy because all the data is already computed in `HCTSA_Empirical1000.mat` on [figshare](https://figshare.com/articles/1000\_Empirical\_Time\_series/5436136) :relaxed:

For example, say we want to find neighbors of the scaling exponent estimated by detrended fluctuation analysis (DFA).
In _hctsa_, DFA is implemented in `SC_FluctAnal.m`, and the standard DFA exponent (root-mean-square fluctuations, with linear detrending, over 50 logarithmically spaced timescales) is the feature `SC_FluctAnal_2_dfa_50_1_logi_alpha`.
(Earlier versions of _hctsa_ also included the `fastdfa` algorithm from [Max Little's website](http://www.maxlittle.net/software/index.php), as `SC_FastDFA`; it was removed in v3.0.0 because the DFA in `SC_FluctAnal` covers it.)
We can find the ID of this feature by finding the matching row in the `Operations` table:

```
[TS_DataMat,TimeSeries,Operations] = TS_LoadData('HCTSA_Empirical1000.mat'); % load data
dfaID = Operations.ID(strcmp(Operations.Name,'SC_FluctAnal_2_dfa_50_1_logi_alpha'))
```

and then find similar features using [`TS_SimSearch`](sim\_search.md), e.g., as:

```
TS_SimSearch('targetID',dfaID,'tsOrOps','ops','whatData','HCTSA_Empirical1000.mat','whatPlots',{'scatter','matrix','network'})
```

The `'network'` plot shows the target and its closest matches (up to 35) as a network.

The figures below show the result of this analysis for `SC_FastDFA_exponent`, made with an older version of _hctsa_ (before `SC_FastDFA` was removed):

![](../../.gitbook/assets/SC\_fastDFA\_scatters.png) ![](../../.gitbook/assets/SC\_fastDFA\_matrix.png)

In that analysis, other features in the library had strong relationships to the `fastdfa` exponent, including some unexpected relationships with the stationarity estimate, `StatAvl25` (the `StatAv` features of v3.0.0 are `StatAvl50` and `StatAvl250`).

Combining the network visualization with scatter plots produces the figures in [our original paper on the empirical structure of time series and their methods](https://doi.org/10.1098/rsif.2013.0048) (cf. Sec. 2.4 of its supplementary text), see below:

![](../../.gitbook/assets/ApEn\_network.png)

Specific pairwise relationships can be probed in more detail (visualizing the types of time series that drive any relationship) using `TS_Plot2d`, e.g., as:

```
% Names of the two features of interest (e.g., the target and one of its matches):
featureNames = {'SC_FluctAnal_2_dfa_50_1_logi_alpha','StatAvl50'};
[~,theColumns] = ismember(featureNames,Operations.Name); % their columns in TS_DataMat
featureData = TS_DataMat(:,theColumns); % take the subset
annotateParams = struct('n',6); % annotate six time series with the cursor
% Generate an annotated 2-dimensional scatter plot:
TS_Plot2d(featureData,TimeSeries,featureNames,annotateParams);
```

This shows the two features against each other, with their distributions shown above and to the right of the plot, and you click on six points to annotate them with their time series.
An example, made with an older version of _hctsa_ for `SC_FastDFA_exponent`, is shown below:

![](../../.gitbook/assets/SC\_fastDFA\_2d.png)
