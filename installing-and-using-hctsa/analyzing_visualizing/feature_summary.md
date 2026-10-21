# Investigating specific operations

Sometimes it's useful to be able to investigate the behavior of an individual operation (or feature) across a time-series dataset. What are the distribution of outputs, and what types of time series receive low values, and what receive high values?

These types of simple questions for specific features of interest can be investigated using the `TS_FeatureSummary` function.
Its inputs are `TS_FeatureSummary(opID,whatData,doViolin,doInspect,annotateParams,customAnnotation)`: the operation ID, the data to use (`'raw'`, i.e., `HCTSA.mat`, by default), whether to draw a violin plot (`true` by default) or kernel-smoothed distributions, whether to make an interactive plot (`false` by default), custom annotation parameters, and an optional custom annotation function.
It plots the distribution of outputs from that operation across the dataset, and annotates time series onto that plot.

Operation IDs depend on the version of the _hctsa_ feature library used to make your HCTSA file, and the IDs below are those of the library used to make the figures.
To find the ID of a feature in your own data, search the `Operations` table by name, e.g., `Operations(strcmp(Operations.Name,'FC_LocalSimple_mean3_ac1'),:)`.

For example, the following:

```
TS_FeatureSummary(100,'raw',false)
```

Produces the following plot of kernel-smoothed distributions (`doViolin` set to `false`), where 6 points on the distribution have been clicked on to annotate them with short time-series segments (`annotateParams.n` sets how many points you click on; the default is 15):

![](../../.gitbook/assets/TS\_FeatureSummary.png)

You can visually see that time series with more autocorrelated patterns through time receive higher values from this operation. Because groups have not been assigned to this dataset, the time series are colored at random.

Running `TS_FeatureSummary` in violin plot mode (the default) provides another representation:

```
annotateParams = struct('maxL',500,'n',10);
TS_FeatureSummary(4310,'raw',true,false,annotateParams);
```

This plots the distribution of feature 4310 from `HCTSA.mat` as a violin plot, with ten 500-sample time-series segments annotated at evenly spaced ranks through the distribution, shown to the right of the plot (without setting `n`, 15 time series are annotated):

![](../../.gitbook/assets/TS\_FeatureSummary\_violin.png)

Setting `doInspect` to `true` (with the violin plot) gives an interactive version instead: click near a point in the distribution to plot that time series below it.

## Plotting for labeled groups of time series

When time series groups have been labeled (using [`TS_LabelGroups`](grouping.md), e.g., as: `TS_LabelGroups('raw',{'noisy','periodic'});`), `TS_FeatureSummary` will plot the distribution for each class separately, as well as an overall distribution.
Annotated points can then be added to each class-specific distribution.
In the example shown below, we can see that the 'noisy' class (red) has low values for this feature (`CO_TC3_2_denom`), whereas the 'periodic' class mostly has high values.

![](../../.gitbook/assets/TS\_FeatureSummary\_grouped.png)

## Simpler distributions

`TS_SingleFeature` provides a simpler way of seeing the class distributions without annotations, as either kernel-smoothed distributions, as in `TS_FeatureSummary`, or as violin plots (`makeViolin` is `true` by default).
It requires group labels to have been assigned to the data.
See below for example implementations:

```
opID = 500;
makeViolin = false;
TS_SingleFeature('raw',opID,makeViolin);
```

Shows the distributions with classification bar underneath (for where a linear classifier would classify different parts of the space as either noisy or periodic):

![](../../.gitbook/assets/TS\_SingleFeature\_1.png)

```
opID = 500;
makeViolin = true;
TS_SingleFeature('raw',opID,makeViolin);
```

Shows the distributions shown as a violin plot, with means annotated and classification bar to the left:

![](../../.gitbook/assets/TS\_SingleFeature\_2.png)

Note that the title gives an indication of the 10-fold cross-validated accuracy (balanced accuracy if the classes are unbalanced) of a linear discriminant classifier in the space.
This is computed from a single 10-fold split and is stochastic, so, as shown above, it can yield slightly different results when repeated.
For a more rigorous analysis than this simple indication, the procedure should be repeated many more times to give a converged estimate of the classification accuracy.
