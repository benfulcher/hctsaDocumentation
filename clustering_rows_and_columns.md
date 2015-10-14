# Clustering the data matrix using `TS_cluster`
<!--{#sec:clustering}-->

For the purposes of visualizing the data matrix, it is often desirable to have the rows and columns reordered to put similar rows adjacent to one another, and similarly to place similar columns adjacent to one another.
This reordering can be done using hierarchical linkage clustering, by the function `TS_cluster`:

```matlab
distanceMetricRow = 'euclidean'; % time-series feature distance
linkageMethodRow = 'average'; % linkage method
distanceMetricCol = 'corr_fast'; % a (poor) approximation of correlations with NaNs
linkageMethodCol = 'average'; % linkage method
    
TS_cluster(distanceMetricRow, linkageMethodRow, distanceMetricCol, linkageMethodCol);
```

This function reads in the data from `HCTSA_N.mat`, and stores the re-ordering of rows and columns back into `HCTSA_N.mat`.
Visualization functions (including `TS_plot_DataMatrix`) can read this in directly, using the general input label `'cl'`.