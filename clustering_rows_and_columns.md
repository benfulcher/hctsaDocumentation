# Clustering the data matrix using `TSQ_cluster`
<!--{#sec:clustering}-->

For the purposes of visualizing the data matrix, it is often desirable to have the rows and columns reordered to put similar rows adjacent to one another, and similarly to place similar columns adjacent to one another.
This reordering can be done by the function `TSQ_cluster`, which has five inputs:

```matlab
    distanceMetricRow = 'euclidean'; % time-series feature distance
    linkageMethodRow = 'average'; % linkage method
    distanceMetricCol = 'corr_fast'; % a (poor) approximation of correlations with NaNs
    linkageMethodCol = 'average'; % linkage method
    
    TSQ_cluster(distanceMetricRow, linkageMethodRow, distanceMetricCol, linkageMethodCol);
```

Note that `TSQ_cluster` uses the mechanics of a more general unsupervised clustering function **TSQ_us_cluster**, where more information about choices for clustering algorithms (for *ClusterMethRow* and *ClusterMethCol*) and the cells of parameters (for *ClusterParamsRow* and *ClusterParamsCol*) can be found.
Note that the output of this function is to local Matlab files; in the same way that `TSQ_normalize` takes in **HCTSA_loc.mat** and outputs normalized versions as **HCTSA\_N.mat**, `TSQ_cluster` takes in **HCTSA\_N.mat** and writes output to a new file: **HCTSA\_cl.mat**, containing clustered (re-ordered) versions of the data in **HCTSA\_N.mat**.