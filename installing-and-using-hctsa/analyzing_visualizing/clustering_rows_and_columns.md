# Clustering rows and columns

For the purposes of visualizing the data matrix, it is often desirable to have the rows and columns reordered to put similar rows adjacent to one another, and similarly to place similar columns adjacent to one another. This reordering can be done using hierarchical linkage clustering, by the function `TS_Cluster`:

```text
distanceMetricRow = 'euclidean'; % time-series feature distance
linkageMethodRow = 'average'; % linkage method
distanceMetricCol = 'corr_fast'; % a (poor) approximation of correlations with NaNs
linkageMethodCol = 'average'; % linkage method

TS_Cluster(distanceMetricRow, linkageMethodRow, distanceMetricCol, linkageMethodCol);
```

This function reads in the data from `HCTSA_N.mat`, and stores the re-ordering of rows and columns back into `HCTSA_N.mat` in the `ts_clust` and `op_clust` \(and, if the size is manageable, also the pairwise distance information\). Visualization functions \(such as [`TS_PlotDataMatrix`](visualizing_the_data_matrix.md) and [`TS_SimSearch`](sim_search.md)\) can then take advantage of this information, using the general input label `'cl'`.

