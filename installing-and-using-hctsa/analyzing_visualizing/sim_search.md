# Finding nearest neighbors

While the global structure of a time-series dataset can be investigated by plotting the data matrix ([`TS_PlotDataMatrix`](visualizing\_the\_data\_matrix.md)) or a low-dimensional representation of it ([`TS_PlotLowDim`](low\_dim.md)), sometimes it can be more interesting to retrieve and visualize relationships between a set of nearest neighbors to a particular time series of interest.

The _hctsa_ framework provides a way to easily compute distances between pairs of time series, e.g., as a Euclidean distance between their normalized feature vectors. This allows very different time series (in terms of their origin, their method of recording and measurement, and their number of samples) to be compared straightforwardly according to their properties, measured by the algorithms in our _hctsa_ library.

For this, we use the `TS_SimSearch` function, whose inputs are all name–value pairs.
The ID of the time series of interest (i.e., the `ID` field of the `TimeSeries` table) is specified with the `'targetID'` input specifier (default: 1), and the number of neighbors with the `'numNeighbors'` input specifier (default: 20; set it to 0 to retrieve all), e.g., `TS_SimSearch('targetID',30,'numNeighbors',10)`.
By default, data is loaded from `HCTSA_N.mat`, but a custom source can be specified using the `'whatData'` input specifier (e.g., `TS_SimSearch('whatData','HCTSA_custom.mat')`).
To search for operations (features) similar to a target operation instead, set `'tsOrOps'` to `'ops'` (the target is then an operation ID).

After specifying the target and how many neighbors to retrieve, `TS_SimSearch` outputs the list of neighbors and their distances to screen, and the function also provides a range of plotting options to visualize the neighbors. The plots to produce are specified as a cell using the 'whatPlots' input.

## Pairwise similarity matrix of neighbors

```
    TS_SimSearch('whatPlots',{'matrix'});
```

To investigate the pairwise relationships between all neighbors retrieved, you specify the `'matrix'` option of the `TS_SimSearch` function.
As an example, we use a publicly available [EEG dataset](http://epileptologie-bonn.de/cms/front\_content.php?idcat=193\&lang=3), keeping (with `TS_FilterData`) its 300 time series from three classes, which we labeled (using [`TS_LabelGroups`](grouping.md) as: `TS_LabelGroups('raw',{'seizure','eyesOpen','eyesClosed'})`) and normalized (using `TS_Normalize`).
The output from retrieving 14 neighbors of the time series with `ID = 1` (an eyes-open recording, `Z001`), as `TS_SimSearch('targetID',1,'whatPlots',{'matrix'},'numNeighbors',14)`, is shown below:

![](../../.gitbook/assets/TS\_SimSearch\_matrix.png)

The specified target time series (`ID = 1`) is shown as a white star in a white box, and all 14 neighbors are shown, labeled on the left of the plot with their IDs and names, alongside the first 100 samples of their time traces.

Pairwise distances are computed between all pairs of time series (as a Euclidean distance between their feature vectors), and plotted using color, from low (red = more similar pairs of time series) to high (blue = more different pairs of time series).

Because the time series have been labeled, the function shows these class assignments using color labels to the left of the matrix (green for `seizure`, orange for `eyesOpen`, and purple for `eyesClosed` in this case).

In this case, 10 of the 14 nearest neighbors of the eyes-open target are also eyes-open recordings (orange), and the other 4 are eyes-closed recordings (purple); none of the seizure recordings (green) are among its neighbors.
Under this distance metric, the eyes-closed neighbors are more similar to each other (and to one eyes-open recording, `Z003`) than to most of the eyes-open neighbors.

## Network of neighbors

Another way to visualize the similarity (under our feature-based distance metric) of all pairs of neighbors is using a network visualization. This is specified as:

```
    TS_SimSearch('targetID',1,'whatPlots',{'network'});
```

which shows the target and its 20 nearest neighbors (the default `'numNeighbors'`), producing something like the following:

![](../../.gitbook/assets/TS\_SimSearch\_network.png)

The strongest links are visualized as blue lines (by default, the strongest 40% of links are plotted, in four shades, as listed in the legend: the strongest 10%, 10–20%, 20–30%, and 30–40% of links).
The layout of the network starts from random node positions, so it differs slightly from run to run (unless the random seed is fixed, e.g., with `rng(0)`).

The target is distinguished (as green in this case), and the other classes of time series are shown using color, with names and time-series segments annotated.
Here, 16 of the 20 neighbors are eyes-open recordings (red), and the 4 eyes-closed recordings (blue) sit together at the edge of the network.

## Scatter plots of the target and its neighbors

```
    TS_SimSearch('targetID',1,'whatPlots',{'scatter'});
```

The scatter setting visualizes the relationship between the target and each of the (up to) 12 time series with the most similar properties to the target.
Each subplot is a scatter of the (normalized) outputs of each feature for the specified target (x-axis) and the match (y-axis).
Clicking on a scatter plot switches between plotting the values and their ranks.
An example is shown below.

![](../../.gitbook/assets/TS\_SimSearch\_scatters.png)

## Other details

Multiple output plots can be produced simultaneously by specifying many types of plots as follows:

```
    TS_SimSearch('targetID',1,'whatPlots',{'matrix','network','scatter'})
```

This produces a plot of each type.

Note that pairwise distances can be pre-computed and saved in the `HCTSA*.mat` file using `TS_PairwiseDist` for custom distance metrics (which is done by default in `TS_Cluster` for datasets containing at most 1000 objects).
`TS_SimSearch` checks for this information in the specified input data (containing the `ts_clust` or `op_clust` structure), and uses it to retrieve neighbors.
If distances have not previously been computed, distances from the target are computed within `TS_SimSearch` as Euclidean distances (time series) or `'corr_fast'` correlation distances, 1 − correlation (operations), between feature vectors.
Another metric can be chosen with the `'whatDistMetric'` input: `'Euclidean'`, `'Spearman'`, `'Pearson'` (or `'corr'`), `'abscorr'` (1 − the absolute Spearman correlation), or `'corr_fast'`.
When distances are stored in the file, they (and the metric they were computed with) are used, and `'whatDistMetric'` is ignored.
