### Clustering the data matrix using `TSQ_cluster`
<!--{#sec:clustering}-->

For the purposes of visualizing the data matrix, it is often desirable to have the rows and columns reordered to put similar rows adjacent to one another, and similarly to place similar columns adjacent to one another.
This reordering can be done by the function `TSQ_cluster`, which has five inputs:

    TSQ_cluster(ClusterMethRow, ClusterParamsRow, ClusterMethCol, ClusterParamsCol, SubSet);

The inputs are as follows:

-   *ClusterMethRow*: a string that sets how to cluster the rows. The usual choice is linkage clustering, which is set using ‘linkage’. Can set to ‘none’ for no clustering of the rows.

-   *ClusterParamsRow*: a cell that defines parameters of the clustering method specified as *ClusterMethRow*. A typical choice for linkage clustering is `{‘euclidean’,‘average’,0,[],0}`, which uses euclidean distances and average linkage clustering.

-   *ClusterMethCol*: a string that sets how to cluster the columns. The most typical choice is ‘linkage’, but can set to ‘none’ for no column clustering.

-   *ClusterParamsCol*: a cell defining parameters of the clustering method specified as *ClusterMethCol*. A typical choice for linkage clustering is `{‘corr’,‘average’,0,[],0}`, which uses linear correlation distances between operations and average linkage clustering.

-   *SubSet*: Allows one to cluster only a specified subset of the columns and rows in **HCTSA\_N.mat**. An example usage is `{‘norm’,[1,5,6,7],[]}`, which would keep only row indices 1,5,6, and 7, and all columns from **HCTSA\_N.mat**.

Note that `TSQ_cluster` uses the mechanics of a more general unsupervised clustering function **TSQ_us_cluster**, where more information about choices for clustering algorithms (for *ClusterMethRow* and *ClusterMethCol*) and the cells of parameters (for *ClusterParamsRow* and *ClusterParamsCol*) can be found.
Note that the output of this function is to local Matlab files; in the same way that `TSQ_normalize` takes in **HCTSA_loc.mat** and outputs normalized versions as **HCTSA\_N.mat**, `TSQ_cluster` takes in **HCTSA\_N.mat** and writes output to a new file: **HCTSA\_cl.mat**, containing clustered (re-ordered) versions of the data in **HCTSA\_N.mat**.