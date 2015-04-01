# Clustering the data matrix using `TSQ_cluster`
<!--{#sec:clustering}-->

For the purposes of visualizing the data matrix, it is often desirable to have the rows and columns reordered to put similar rows adjacent to one another, and similarly to place similar columns adjacent to one another.
This reordering can be done using hierarchical linkage clustering, by the function `TSQ_cluster`:

```matlab
distanceMetricRow = 'euclidean'; % time-series feature distance
linkageMethodRow = 'average'; % linkage method
distanceMetricCol = 'corr_fast'; % a (poor) approximation of correlations with NaNs
linkageMethodCol = 'average'; % linkage method
    
TSQ_cluster(distanceMetricRow, linkageMethodRow, distanceMetricCol, linkageMethodCol);
```

This function reads in the data from **HCTSA_N.mat**, reorders the rows and columns, and then outputs the new re-ordered data to **HCTSA_cl.mat**.
Visualization functions (including `TSQ_plot_DataMatrix`) can read this in directly, using the general input label `'cl'`.

Note that `TSQ_cluster` uses the mechanics of a more general unsupervised clustering function **TSQ_ClusterReorder** for performing the clustering.
