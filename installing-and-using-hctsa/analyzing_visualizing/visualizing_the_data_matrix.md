# Visualizing the data matrix

The clustered data matrix (if clustering has been performed, otherwise the non-clustered data matrix is used) can be visualized by running `TS_PlotDataMatrix`.

This will produce a colored visualization of the data matrix such as that shown below.

When data is grouped according to a set of distinct keywords and stored as group metadata (using the `TS_LabelGroups` [function](grouping.md)), these can also be visualized using `TS_PlotDataMatrix('colorGroups',true)`.

All inputs to `TS_PlotDataMatrix` are name–value pairs:

* `'whatData'`: the data to plot: `'norm'` (default, `HCTSA_N.mat`), `'raw'` (`HCTSA.mat`), or a filename.
* `'addTimeSeries'`: whether to annotate time-series segments to the left of the data matrix (default: `true`).
* `'timeSeriesLength'`: the number of samples in each of these annotations (default: 200).
* `'colorGroups'`: whether to color each labeled group of time series with its own colormap (default: `false`).
  The values of each group are scaled to span their own colormap, so colors can only be compared within a group.
* `'groupReorder'`: whether to sort time series by group, and cluster within each group (default: `false`).
* `'customColorMap'`: the colormap for ungrouped data (default: `'redyellowblue'`).
* `'colorNaNs'`: whether to draw rectangles over special values (default: `true`).
* `'customOrder'`: a cell of two permutation vectors, `{rowOrder,columnOrder}`, to reorder rows and columns before plotting.

### Visualizing the normalized data matrix

As an example, we take 15 time series from the Bonn EEG dataset: five each of the eyes-open (`Z001`–`Z005`), eyes-closed (`O001`–`O005`), and seizure (`S001`–`S005`) recordings, kept from the full _hctsa_ dataset using `TS_FilterData` and normalized using `TS_Normalize` (the default `'mixedSigmoid'` transformation), which leaves 6074 features.
Running `TS_PlotDataMatrix('whatData','norm')` (or just `TS_PlotDataMatrix`, as `'norm'` is the default) plots the data contained in the local file `HCTSA_N.mat`, yielding:

![](../../.gitbook/assets/Bonn\_DataMatrix\_norm.png)

where values are shown from low (blue) to high (red) after normalization.
Any special values (e.g., `NaN`) are drawn as black rectangles (there are none here, as `TS_Normalize` removed the features with special values).
Due to the size of the matrix, operations are not labeled individually.

Examples of time-series segments (the first 200 samples of each) are shown to the left of the plot, and when the data matrix is zoomed (here to the five seizure recordings and operations 1000–4000), the time-series annotations remain matched to the data matrix:

![](../../.gitbook/assets/Bonn\_DataMatrix\_zoomed.png)

### Visualizing the clustered data matrix

It can be useful to display the matrix with the order of time series and operations preserved, but the relationships between rows and columns can be difficult to visualize when ordered randomly.

If you have run [`TS_Cluster`](clustering\_rows\_and\_columns.md), it will save clustering information back to the HCTSA file, that will be loaded if possible and used to reorder rows and columns by similarity (if this information exists). Here's an example of running `TS_PlotDataMatrix` on data that has this clustering info:

![](../../.gitbook/assets/Bonn\_DataMatrix\_clustered.png)

Much prettier, hey?!
By reordering rows and columns, this representation reveals correlated patterns of outputs across different types of operations, and similar sets of properties between different types of time series.
Here, the five seizure recordings are placed together at the top, with a distinctive pattern of feature values, while the eyes-closed recordings `O002`–`O005` form their own group, and `O001` is placed among the eyes-open recordings.

## Incorporating group information

In this example, we consider a set of 20 periodic signals (each a sum of one or two sinusoids) and 20 noisy periodic signals (drawn in the same way, plus Gaussian noise).
We assigned the time series in `HCTSA.mat` to groups (using `TS_LabelGroups('raw',{'periodic','noisy'})`), then normalized the data matrix (`TS_Normalize`), and then clustered it (`TS_Cluster`).
So now we have a clustered data matrix containing thousands of summaries of each time series, as well as pre-assigned group information as to which time series are periodic and which are noisy.
When the time series have been assigned to groups, this can be accessed by switching on the `'colorGroups'` setting:

```
TS_PlotDataMatrix('whatData','norm','colorGroups',false); % don't color according to group labels
TS_PlotDataMatrix('whatData','norm','colorGroups',true); % color according to group labels
```

producing the following two plots:

![](../../.gitbook/assets/DataMatrix\_together.png)

When group information is _not used_ (the left plot), the data is visualized in the default blue/yellow/red color scheme, but when the assigned groups are colored (right plot; labeled on the colorbar), we see that the clustered dataset separates perfectly into the noisy (red) and periodic (blue) time series, and we can visualize the features that contribute to the separation.
