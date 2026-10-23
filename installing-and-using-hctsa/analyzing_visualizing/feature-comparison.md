# Comparing to existing features

One of the key goals of highly comparative time-series analysis, is to allow unbiased methodological comparison between the vast literature of time-series analysis tools developed for different applications. By representing features in terms of their outputs across a time-series dataset, the context of a given feature can be assessed by searching the database for features with similar behavior. The search can be done using a diverse range of real and model-generated data, or using a more specific dataset if this is more appropriate for a given application (e.g., looking just at EEG signals). Just like [similar time series to a target can be retrieved and visualized](sim\_search.md), similar features to a given target feature can also be retrieved using `TS_SimSearch`.

This chapter will give instructions on how you can compare a new time-series analysis feature to our library of over 6000 time-series features (6392 in _hctsa_ v3.0.0) using _hctsa_.
We assume that the reader has [installed _hctsa_](../setup/), which will be required to work with files and compute features.

## Setting a data context

The first step is defining the set of features to compare to (here we use the default _hctsa_ library), and the set of time-series data that behavior is going to be assessed on. If you have just developed a new algorithm for time-series analysis and want to see how it performs across a range of interdisciplinary time-series data, then you may want to use a diverse set of time series sampled from across science. This can be easily achieved using our set of 1000 time series, a random selection of 25 such time series are plotted below (only the first 250 samples are plotted to aid visualization):

![](../../.gitbook/assets/Empirical1000\_25\_250samples.png)

The _hctsa_ input file for these time series is included with _hctsa_ (`TimeSeries/INP_Empirical1000.mat`), and the data, input file (as `INP_1000ts.mat`) and a pre-computed feature matrix (`HCTSA_Empirical1000.mat`) can also be downloaded from [figshare](https://doi.org/10.6084/m9.figshare.5436136).
The pre-computed matrix there was computed with a pre-release version of _hctsa_ v3 (v3.0.0-alpha), so its feature names and values differ from those of v3.0.0, and results from different versions should not be mixed.
We therefore recommend recomputing the features with your version of _hctsa_, which also ensures they match your local compute architecture: initialize with `TS_Init('INP_Empirical1000.mat');`, then compute using [`TS_Compute`](../calculating/running\_computations.md) (e.g., `TS_Compute('series')`, which parallelizes the computation over time series).

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

<details>

<summary>The <code>MyHotFeature.m</code> used for the results on this page</summary>

To produce the results shown on this page, we wrote a stand-in `MyHotFeature.m`, whose `hotFeature1` is the first time lag at which the autocorrelation function of `x` falls below zero, computed lag by lag in the time domain.
In the rest of this example, we pretend not to know this, and see what _hctsa_ can tell us about it.

```matlab
function out = MyHotFeature(x)
x = x(:);
N = length(x);
xc = x - mean(x);
variance = sum(xc.^2);

out = struct();
out.hotFeature1 = NaN;
if variance == 0
    return % constant series: autocorrelation undefined
end

for tau = 1:N-1
    acf_tau = sum(xc(1:N-tau).*xc(1+tau:N))/variance;
    if acf_tau < 0
        out.hotFeature1 = tau;
        return
    end
end
end
```

</details>

So now we are able to initiate a new _hctsa_ calculation, specifying custom code calls (_master_) and features to extract from the code call (_features_), as:

```
TS_Init('INP_Empirical1000.mat',{'INP_hot_master.txt','INP_hot_features.txt'},false,'HCTSA_hot.mat');
```

(Setting the third input to `true` instead prints how each input file was read, and asks you to confirm each one.)
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
The merged file, `HCTSA_merged.mat`, contains 1000 time series and 6393 features.

### 3. Comparing

Now that we have all of the data in the same HCTSA file, we can compare the behavior of the new feature to the existing library of features.
This can be done manually by the researcher, or by using standard _hctsa_ functions; the most relevant is [`TS_SimSearch`](sim\_search.md).
We can find the ID assigned to our new `hot_feature1` in the merged HCTSA file as:

```
load('HCTSA_merged.mat','Operations');
hotID = Operations.ID(strcmp(Operations.Name,'hot_feature1'))
```

which gives `hotID = 6393`: `TS_Combine` re-indexes the features of the merged file, so the new feature's ID follows those of the 6392 library features.
Then we can use [`TS_SimSearch`](sim\_search.md) to explore the relationship of our hot new feature to other features in the _hctsa_ library:

```
TS_SimSearch('targetID',hotID,'tsOrOps','ops','whatData','HCTSA_merged.mat',...
                'whatDistMetric','corr','whatPlots',{'scatter','matrix'})
```

For features, `TS_SimSearch` measures distances as 1 − _r_, for Pearson correlation coefficients, _r_, so features that are strongly anticorrelated with the target are not counted as matches.
By default it uses a fast approximation to _r_ (`'corr_fast'`); here we set `'whatDistMetric','corr'` to compute _r_ exactly (for each pair of features, using the time series where both have values).
To find features that are strongly correlated with the target in either direction, set `'whatDistMetric','abscorr'` (distances 1 − |ρ|, for Spearman correlation coefficients, ρ).
(If the data contain distances stored by `TS_Cluster`, those are used instead.)

{% hint style="warning" %}
In _hctsa_ v3.0.0, use `'whatDistMetric','corr'` (as here) when searching for features in an unnormalized HCTSA file.
The `'corr_fast'` approximation is computed in single precision, which overflows for features with very large values (above about 3 × 10<sup>38</sup>, as for some of the raw higher-order moments, `DN_Moments_raw_5` to `DN_Moments_raw_11`, on the Empirical1000 time series).
This corrupts the list of matches: for example, the target can be listed as its own first match.
{% endhint %}

`TS_SimSearch` prints the closest matches (20 by default; set `'numNeighbors'`) to screen, each with its ID, name, keywords and correlation to the target:

```
---TARGET: [6393] hot_feature1 (hot,science)---
Computing Pearson distances to 6392 other objects... Done.
1. [524] firstZero_acf_tau (correlation,timescale): rho = 1.00
2. [525] firstZero_acf_point (correlation,timescale): rho = 1.00
3. [4027] PH_Walker_prop_11_w_tau (trend): rho = 1.00
4. [3951] PH_Walker_momentum_2_w_tau (trend): rho = 1.00
5. [297] CO_AutoCorrShape_posDrown_Nac (correlation): rho = 0.99
6. [4008] PH_Walker_prop_05_w_tau (trend): rho = 0.99
7. [3970] PH_Walker_momentum_5_w_tau (trend): rho = 0.99
8. [4046] PH_Walker_runningvar_15_50_w_tau (trend): rho = 0.99
9. [3989] PH_Walker_prop_01_w_tau (trend): rho = 0.96
10. [291] CO_AutoCorrShape_drown_sumacf (correlation): rho = 0.94
...
16. [526] firstCrossing_1e_acf_tau (correlation,timescale): rho = 0.89
...
20. [6172] SY_SpreadRandomLocal_ac2_100_stdtaul (stationarity): rho = 0.87
```

(The printed `rho` is the correlation coefficient, here Pearson's _r_.)
The `'scatter'` plot shows scatter plots of the target feature against each of its closest matches (up to 12), titled by their distance, _d_ = 1 − _r_, and you can click on a plot to switch between values and ranks.

Here, our feature reproduces the behavior of the first zero-crossing of the autocorrelation function, `firstZero_acf_tau` (match 1), exactly: the two are equal for all 1000 time series.
It is also almost perfectly correlated with the interpolated version of the same crossing point (`firstZero_acf_point`), and with the autocorrelation timescales of the series' "walker" transformations (`PH_Walker_*_w_tau`).
See [Interpreting Features](interpreting-features.md) for more info on how to interpret matching features.

![](<../../.gitbook/assets/hot_feature1_scatters.png>)

The `'matrix'` plot shows the pairwise distances between the target (marked with an asterisk) and its matches, reordered by clustering, and provides another visualization of the context of this hot new feature.
Here it shows a tight block of near-identical autocorrelation timescales around `hot_feature1` (matches 1–8, with _r_ ≥ 0.99), with more distantly related groups, such as the 1/_e_ crossing of the autocorrelation function (`firstCrossing_1e_acf_tau`, `firstCrossing_1e_acf_point`) and some stationarity and trend features (`SY_Trend_stdYC`, `SY_DriftingMeanCUSUM_maxBridge`):

![](<../../.gitbook/assets/hot_feature1_matrix.png>)

### 4. Interpreting

In this case, the hot new feature wasn't so hot: it was highly (linearly) correlated to many existing features (and identical to the simple zero-crossing of the autocorrelation function, `firstZero_acf_tau`), even across a highly diverse time-series dataset. However, if you have more luck and come up with a hot new feature that shows distinctive (and useful) performance, then it can be incorporated in the default set of features used by _hctsa_ by adding the necessary master and feature definitions (i.e., the text in `INP_hot_master.txt` and the text in `INP_hot_features.txt`) to the library files (`INP_mops_hctsa.txt` and `INP_ops_hctsa.txt` in the **FeatureSets** directory of _hctsa_), as explained [here](../calculating/input_files.md). You might even celebrate your success by sharing your new feature with the community, by sending a [Pull Request](https://help.github.com/articles/using-pull-requests/) to the [hctsa github repository](https://github.com/benfulcher/hctsa)!! :satisfied:

## _EXAMPLE 2_: Determining the relationship between an existing _hctsa_ feature and the rest of the library.

If you have computed the feature matrix for the 1000 empirical time series, `HCTSA_Empirical1000.mat` (as described above), then this is easy, because all the features have already been computed :relaxed:

For example, say we want to find neighbors of the scaling exponent estimated by detrended fluctuation analysis (DFA).
In _hctsa_, DFA is implemented in `SC_FluctAnal.m`, and the standard DFA exponent (root-mean-square fluctuations, with linear detrending, over 50 logarithmically spaced timescales) is the feature `SC_FluctAnal_2_dfa_50_1_logi_alpha`.
(Earlier versions of _hctsa_ also included the `fastdfa` algorithm from [Max Little's website](http://www.maxlittle.net/software/index.php), as `SC_FastDFA`; it was removed in v3.0.0 because the DFA in `SC_FluctAnal` covers it.)
We can find the ID of this feature by finding the matching row in the `Operations` table:

```
[TS_DataMat,TimeSeries,Operations] = TS_LoadData('HCTSA_Empirical1000.mat'); % load data
dfaID = Operations.ID(strcmp(Operations.Name,'SC_FluctAnal_2_dfa_50_1_logi_alpha'))
```

which gives `dfaID = 5135`, and then find similar features using [`TS_SimSearch`](sim\_search.md), e.g., as:

```
TS_SimSearch('targetID',dfaID,'tsOrOps','ops','whatData','HCTSA_Empirical1000.mat',...
                'whatDistMetric','corr','whatPlots',{'scatter','matrix','network'})
```

which prints:

```
---TARGET: [5135] SC_FluctAnal_2_dfa_50_1_logi_alpha (scaling)---
Computing Pearson distances to 6391 other objects... Done.
1. [5267] SC_FluctAnal_2_rsrange_50_logi_alpha (scaling): rho = 1.00
2. [5289] SC_FluctAnal_2_rsrangefit_50_1_logi_alpha (scaling): rho = 0.99
3. [5391] SC_MFDFA__n5_5_1_h2 (scaling,fractal,multifractal): rho = 0.99
4. [5157] SC_FluctAnal_2_dfa_50_2_logi_alpha (scaling): rho = 0.95
5. [5392] SC_MFDFA__n5_5_1_alphaMin (scaling,fractal,multifractal): rho = 0.94
6. [5091] SC_FluctAnal_2_dfa_50_1_2_logi_alpha (scaling): rho = 0.94
7. [5921] StatAvl50 (stationarity,statAv): rho = 0.94
8. [6157] SY_SpreadRandomLocal_100_100_stdmean (stationarity): rho = 0.93
9. [5355] SC_FluctAnal_sign_2_dfa_50_2_logi_alpha (scaling): rho = 0.92
10. [5113] SC_FluctAnal_2_dfa_50_1_3_logi_alpha (scaling): rho = 0.92
11. [914] CP_ML_StepDetect_l1pwc_10_rmsoff (stepDetection): rho = 0.90
12. [5311] SC_FluctAnal_2_std_50_logi_alpha (scaling): rho = 0.90
...
15. [5922] StatAvl250 (stationarity,statAv): rho = 0.89
...
20. [5245] SC_FluctAnal_2_range_50_logi_alpha (scaling): rho = 0.88
```

The scatter plots and the distance matrix for the DFA exponent are shown below:

![](../../.gitbook/assets/DFA\_alpha\_scatters.png) ![](../../.gitbook/assets/DFA\_alpha\_matrix.png)

As expected, the closest matches are other estimates of the scaling exponent: from the rescaled range (`SC_FluctAnal_2_rsrange_50_logi_alpha`, _r_ = 1.00), from multifractal DFA (`SC_MFDFA__n5_5_1_h2`, _r_ = 0.99), and from DFA with quadratic detrending (`SC_FluctAnal_2_dfa_50_2_logi_alpha`) or of the time series subsampled at lag 2 or 3 (`SC_FluctAnal_2_dfa_50_1_2_logi_alpha`, `SC_FluctAnal_2_dfa_50_1_3_logi_alpha`).
But some features from other parts of the library are also strongly related, including the stationarity measures `StatAvl50` (_r_ = 0.94) and `SY_SpreadRandomLocal_100_100_stdmean` (_r_ = 0.93), and a step-detection feature, `CP_ML_StepDetect_l1pwc_10_rmsoff` (_r_ = 0.90).
Their scatter plots (matches 7, 8 and 11) show a nonlinear, sigmoidal relationship with the DFA exponent.

The `'network'` plot shows the target (red) and its closest matches (up to 35) as a network, with links shaded by the strength of the correlation between each pair of features (the legend gives the strongest 10%, 10–20%, 20–30% and 30–40% of pairs; weaker links aren't drawn).
The nodes are placed by a force-directed layout from random initial positions, so set the random seed (e.g., `rng(3,'twister')`, as here) for a reproducible layout:

![](../../.gitbook/assets/DFA\_alpha\_network.png)

Combining network visualizations like this with scatter plots produces the figures in [our original paper on the empirical structure of time series and their methods](https://doi.org/10.1098/rsif.2013.0048) (cf. Sec. 2.4 of its supplementary text), which mapped the neighbors of approximate entropy in this way.

Specific pairwise relationships can be probed in more detail (visualizing the types of time series that drive any relationship) using `TS_Plot2d`.
Here we pick `StatAvl50`, the closest match from outside the family of scaling-exponent features (match 7, _r_ = 0.94).
`StatAvl50` is the standard deviation of the means of consecutive 50-sample windows, relative to the standard deviation of the whole time series.
It is a single-timescale measure of how quickly fluctuations average out, which is the property that the DFA exponent measures across many timescales, so we expect a strong (but nonlinear) relationship between the two:

```
% Names of the two features of interest (the target and match 7):
featureNames = {'SC_FluctAnal_2_dfa_50_1_logi_alpha','StatAvl50'};
[~,theColumns] = ismember(featureNames,Operations.Name); % their columns in TS_DataMat
featureData = TS_DataMat(:,theColumns); % take the subset
annotateParams = struct('n',6); % annotate six time series with the cursor
% Generate an annotated 2-dimensional scatter plot:
TS_Plot2d(featureData,TimeSeries,featureNames,annotateParams);
```

This shows the two features against each other, with their distributions shown above and to the right of the plot, and you click on six points to annotate them with their time series.
For the figure below, the six time series were instead chosen at random, by running `rng(2,'twister')` and setting `annotateParams = struct('n',6,'userInput',false)`:

![](../../.gitbook/assets/DFA\_alpha\_2d.png)

Rapidly fluctuating time series, such as the chaotic Tinkerbell map (DFA exponent 0.15) and spiky financial trading-volume series, have window means that barely vary (`StatAvl50` ≤ 0.25).
Slowly varying series with a DFA exponent near 1.5, such as a chaotic flow (the Rucklidge system) and a stochastic differential equation model, have window means that vary as much as the time series itself (`StatAvl50` ≈ 1).
In between, `StatAvl50` rises steeply with the DFA exponent (between exponents of about 0.5 and 1.2), giving the sigmoidal shape of the relationship.
