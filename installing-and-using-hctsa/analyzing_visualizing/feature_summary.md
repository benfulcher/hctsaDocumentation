# Investigating specific operations

Sometimes it's useful to be able to investigate the behavior of an individual operation (or feature) across a time-series dataset. What are the distribution of outputs, and what types of time series receive low values, and what receive high values?

These types of simple questions for specific features of interest can be investigated using the `TS_FeatureSummary` function.
Its inputs are `TS_FeatureSummary(opID,whatData,doViolin,doInspect,annotateParams,customAnnotation)`: the operation ID, the data to use (`'raw'`, i.e., `HCTSA.mat`, by default), whether to draw a violin plot (`true` by default) or kernel-smoothed distributions, whether to make an interactive plot (`false` by default), custom annotation parameters, and an optional custom annotation function.
It plots the distribution of outputs from that operation across the dataset, and annotates time series onto that plot.

Operation IDs depend on the version of the _hctsa_ feature library used to make your HCTSA file, and the IDs below are those of the default feature set of _hctsa_ v3.0.0 (6392 operations).
To find the ID of a feature in your own data, search the `Operations` table by name, e.g., `Operations(strcmp(Operations.Name,'FC_LocalSimple_mean3_ac1'),:)`.

For example, consider a dataset of 40 time series of length 500: 20 periodic signals (sums of one or two sinusoids, with the keyword `periodic`) and 20 periodic signals with added noise (with the keyword `noisy`), before any groups have been assigned.
The following:

```
TS_FeatureSummary(1,'raw',false,false,struct('n',6,'maxL',150))
```

produces the following plot of the kernel-smoothed distribution (`doViolin` set to `false`) of the lag-1 autocorrelation, `AC_1` (ID 1), where 6 points on the distribution have been clicked on to annotate them with the first 150 samples of their time series (`annotateParams.n` sets how many points you click on; the default is 15):

![](../../.gitbook/assets/TS\_FeatureSummary.png)

You can visually see that time series with more autocorrelated patterns through time (smooth oscillations) receive higher values from this operation, and noisier time series receive lower values.
Because groups have not been assigned to this dataset, each annotated time series is drawn in its own color.

Running `TS_FeatureSummary` in violin plot mode (the default) provides another representation:

```
annotateParams = struct('maxL',500,'n',10);
TS_FeatureSummary(5633,'raw',true,false,annotateParams);
```

For the 1000 time series of the [Empirical1000](https://doi.org/10.6084/m9.figshare.5436136) dataset, this plots the distribution of feature 5633, `SP_Summaries_pgram_hamm_sfm` (the spectral flatness of the Hamming-windowed periodogram, in dB), from `HCTSA.mat` as a violin plot.
Ten time series, at evenly spaced ranks through the distribution, are marked on the violin and their first 500 samples are shown to the right of the plot (without setting `n`, 15 time series are annotated):

![](../../.gitbook/assets/TS\_FeatureSummary\_violin.png)

Setting `doInspect` to `true` (with the violin plot) gives an interactive version instead: click near a point in the distribution to plot that time series below it.

## Plotting for labeled groups of time series

When time series groups have been labeled (using [`TS_LabelGroups`](grouping.md)), `TS_FeatureSummary` will plot the distribution for each class separately, as well as an overall distribution.
Annotated points can then be added to each class-specific distribution.
For example, labeling the noisy and periodic time series from above and plotting feature 767, `CO_TC3_2_denom`:

```
TS_LabelGroups('raw',{'noisy','periodic'});
TS_FeatureSummary(767,'raw',false,false,struct('n',6,'maxL',150))
```

and clicking on three time series from each class, gives the plot below.
We can see that the 'noisy' class (red) has lower values for this feature, whereas the 'periodic' class (blue) mostly has high values (the combined distribution of all 40 time series is shown in gray).

![](../../.gitbook/assets/TS\_FeatureSummary\_grouped.png)

## Simpler distributions

`TS_SingleFeature` provides a simpler way of seeing the class distributions without annotations, as either kernel-smoothed distributions, as in `TS_FeatureSummary`, or as violin plots (`makeViolin` is `true` by default).
It requires group labels to have been assigned to the data.
See below for example implementations:

```
opID = 767;
makeViolin = false;
TS_SingleFeature('raw',opID,makeViolin);
```

Shows the distributions with classification bar underneath (for where a linear classifier would classify different parts of the space as either noisy or periodic):

![](../../.gitbook/assets/TS\_SingleFeature\_1.png)

```
opID = 767;
makeViolin = true;
TS_SingleFeature('raw',opID,makeViolin);
```

Shows the distributions shown as a violin plot, with means annotated and classification bar to the left:

![](../../.gitbook/assets/TS\_SingleFeature\_2.png)

Note that the title gives an indication of the 10-fold cross-validated accuracy (balanced accuracy if the classes are unbalanced) of a linear discriminant classifier in the space, and the mean and standard deviation across folds is printed to the command window, e.g.:

```
[767] CO_TC3_2_denom (correlation,nonlinear)
10-fold cross-validated accuracy: 80.00 +/- 15.81%
```

This is computed from a single 10-fold split and is stochastic, so, as shown above (80.0% and then 77.5% for the same feature), it can yield slightly different results when repeated.
For a more rigorous analysis than this simple indication, the procedure should be repeated many more times to give a converged estimate of the classification accuracy.
