# Working with short time series

Although many sections of the time-series analysis literature has worked to develop methods for quantifying complex temporal structure in long time-series recordings, many time series that are analyzed in practice are relatively short. _hctsa_ has been successfully applied to time-series classification problems in the data mining literature, which includes datasets of time series as short as 60 samples ([link to paper](http://ieeexplore.ieee.org/lpdocs/epic03/wrapper.htm?arnumber=6786425)). However, time-series data are sometimes even shorter, including yearly economic data across perhaps six years, or biological data measured at say 10 points across a lifespan. Although many features in _hctsa_ will not give a meaningful output when applied to a short time series, _hctsa_ includes methods for filtering such features (cf. [`TS_Normalize`](filtering\_and\_normalizing.md)), after which the remaining features can be used for analysis.

To show how many features can be computed as a function of time-series length, we took 10 diverse time series from the [Empirical1000](https://doi.org/10.6084/m9.figshare.5436136) dataset (including Gaussian noise, an AR(4) process, the Lorenz system, a chaotic map, a random walk, heart-rate intervals, financial returns, music, monthly river flow and seismic data) and computed the default _hctsa_ v3.0.0 feature set on the first N samples of each, for N from 5 to 500.
The number of features with a real-valued output (`TS_Quality` = 0) is plotted below against N for each series (gray), with the mean across the 10 series (black) and the number of features that are real-valued for all 10 series (blue); the full set of 6392 features is shown as a dashed horizontal line:

![](../../.gitbook/assets/LengthDependence.png)

Even at N = 5, a mean of 2463 features per series could be computed (2669 at most); one series (a music recording) gave far fewer (1131), which is why only 1102 features were real-valued for all 10 series at that length.
The mean rises to 3464 features at N = 10, 5352 at N = 50, 5751 at N = 100 and 6220 at N = 500 (where 5703 features were real-valued for all 10 series).

Most of the features that cannot be computed on very short time series return a special value (most commonly `NaN`), but some operations raise an error, which is recorded with quality label 1 in `TS_Quality` (see [Performing calculations](../calculating/running\_computations.md)).
In this example, every series of length 50 or less had errors (from 55 master operations in total; at N = 5, 608 operations from 53 master operations), and no series of length 60 or more had any.
They included `SP_Summaries` and `MF_arfit` (errors up to N = 15), `MF_GARCHfit` (up to N = 20), `MF_FitSubsegments` (up to N = 25) and the `ST_MomentCorr` operations (up to N = 50).
These errors are expected for such short inputs, and these features are removed by `TS_Normalize` like any other feature without a real-valued output.

Note that one must be careful when representing a 5-dimensional object with thousands of features, the vast majority of which will be highly intercorrelated.

When computing features for a large number of short time series, `TS_Compute('series')` parallelizes the computation over time series, which is much faster than parallelizing over the operations within each time series (`TS_Compute('operations')`; see [Running computations](../calculating/running\_computations.md)).

## Example application to developmental gene-expression data

To demonstrate the feasibility of running _hctsa_ analysis on datasets of short time series, we applied _hctsa_ to gene expression data in the cerebellar brain region, r1A, across seven developmental time points (from the Allen Institute's [Developing Mouse Brain Atlas](http://developingmouse.brain-map.org)), for a subset of 50 genes.
After filtering and normalizing (`TS_Normalize`), then clustering (`TS_Cluster`), we plotted the clustered time-series data matrix (`TS_PlotDataMatrix`, which uses the clustered ordering when it exists):

![](../../.gitbook/assets/GeneExpressionExample.png)

Inspecting the time series plots to the left of the colored matrix, we can see that genes with similar temporal expression profiles are clustered together based on their 2829-long feature vector representations (the number of features retained after filtering, with the version of _hctsa_ used for this analysis).
Thus, these feature-based representations provide a meaningful representation of these short time series.
Further, while these 2829-long feature vectors are shorter than those that can be computed from longer time series, they still constitute a highly comprehensive representation that can be used as the starting point to obtain interpretable understanding in addressing specific domain questions.
