## Performing highly comparative analysis
<!--{#sec:analyzing}-->

Once the database has been set up, populated with data and operations, and the operations have been computed on the time-series data, the result can be used to perform a wide variety of highly comparative analyses, such as those outlined in [our paper][hctsa-paper].

The types of methods employed on a given dataset should be developed to suit specific time-series analysis tasks.
Setting up the problem, guiding the methodology, and interpreting the results requires strong scientific input that should draw on domain knowledge, the questions asked of the data, experience performing data analysis, and statistical considerations.
Once a highly comparative dataset is produced, users can be creative in their exploration and analysis of the data, or draw upon a library of analytic techniques that we have developed.

The first step of any analysis is to retrieve a relevant portion of data from the database to local Matlab files for analysis.
As described above, this is done using the `TSQ_prepared` function.
Example usage is provided:

    TSQ_prepared(ts_ids, op_ids,'all');

for vectors `ts_ids` and `op_ids`, specifying the ts\_ids and op\_ids to be retrieved from the database. Running the code in this way, using the ‘all’ tag, ensures that the full range of ts\_ids and op\_ids specified are retrieved from the database and stored in the local file, `HCTSA_loc.mat`, which can then form the basis of subsequent analysis.

## Filtering and normalizing the data using TSQ_normalize
<!--{#sec:normalization}-->

The first step in analyzing a dataset involves processing the data
matrix. This involves filtering out operations or time series that
produced many errors or special-valued outputs, and then normalizing of
the output of all operations, which is typically done in-sample,
according to an outlier-robust sigmoidal transform (although other
normalizing transformations can be selected). Both of these tasks are
performed using the function `TSQ_normalize`. Example usage is as
follows:

    TSQ_normalize(`scaledSQzscore',[0.8,1.0]);

The first input controls the normalization method, in this case a scaled
outlier-robust sigmoidal transformation, and the second input controls
the filtering, in this case each time series needs to produce at least
80% good-valued outputs (setting 0.8), or they are removed, and then
operations with less than 100% good-valued outputs are removed (setting
1.0). These can be relaxed, to, for example, [0.7,0.9], which removes
time series with less than 70% good values, and then removes operations
with less than 90% good values. When neither value is 1.0, this can
leave NaN values in the resulting data matrix, which can affect some
calculations that cannot deal with missing values (such as PCA). Some
applications can tolerate some special-valued outputs from operations
(like some clustering methods, where distances are simply calculated
using those operations that are did not produce special-valued outputs
for each pair of objects), but others cannot (like Principal Components
Analysis); the filtering parameters should be specified accordingly.
Similarly, it makes sense to weight each operation equally for the
purposes dimensionality reduction, and thus normalize all operations to
the same range using a transformation like ‘scaledSQzscore’,
‘scaledSigmoid’, ‘mixedSigmoid’. For the case of calculating mutual
information distances between operations, however, one would rather not
distort the distributions and perform no normalization, using ‘raw’ or a
linear transformation like ‘zscore’, for example. The list of
implemented normalization transformations can be found in the function
`BF_NormalizeMatrix`. An example usage:

        TSQ_normalize('scaledSQzscore',[0.8,0.8]);

This filters time series (rows of the data matrix) with more than 20% special-values, then filters out operations (columns of the data matrix) with more than 20% special values, then applies the outlier-robust ‘scaledSQzscore’ sigmoidal transformation to all remaining operations (columns).
Note that this transformation does not tolerate distributions with an interquartile range of zero, which will be filtered out.

Another example:

        TSQ_normalize('raw',[0.8,1]);

This filters time series (rows of the data matrix) with more than 20% special-values, then filters out operations (columns of the data matrix) with any special values whatsoever, leaving a data matrix with well-behaved operations as columns and containing no special or missing values.
No normalizing transformation is applied to the remaining operations.

The `TSQ_normalize` function writes the new, filtered, normalized matrix to a local file called **HCTSA_N**.
This contains normalized, and trimmed versions of the information in `HCTSA_loc.mat`.
Analysis can now be performed on the data contained in `HCTSA_N.mat`, in the knowledge that different settings for filtering and normalizing the results can be applied at any time by simply rerunning `TSQ_normalize`, which will overwrite the existing `HCTSA_N.mat` with the results of the new normalization and filtration settings.

### Clustering the data matrix using TSQ_cluster {#sec:clustering}

For the purposes of visualizing the data matrix, it is often desirable to have the rows and columns reordered to put similar rows adjacent to one another, and similarly to place similar columns adjacent to one another.
This reordering can be done by the function `TSQ_cluster`, which has five inputs:

        TSQ_cluster(ClusterMethRow, ClusterParamsRow, ClusterMethCol, ClusterParamsCol, SubSet);

The inputs are as follows:

-   *ClusterMethRow*: a string that sets how to cluster the rows. The usual choice is linkage clustering, which is set using ‘linkage’. Can set to ‘none’ for no clustering of the rows.

-   *ClusterParamsRow*: a cell that defines parameters of the clustering method specified as *ClusterMethRow*. A typical choice for linkage clustering is `{‘euclidean’,‘average’,0,[],0}`, which uses euclidean distances and average linkage clustering.

-   *ClusterMethCol*: a string that sets how to cluster the columns. The most typical choice is ‘linkage’, but can set to ‘none’ for no column clustering.

-   *ClusterParamsCol*: a cell defining parameters of the clustering method specified as *ClusterMethCol*. A typical choice for linkage clustering is `{‘corr’,‘average’,0,[],0}`, which uses linear correlation distances between operations and average linkage clustering.

-   *SubSet*: Allows one to cluster only a specified subset of the columns and rows in **HCTSA\_N**. An example usage is `{‘norm’,[1,5,6,7],[]}`, which would keep only row indices 1,5,6, and 7, and all columns from **HCTSA\_N**.

Note that `TSQ_cluster` uses the mechanics of a more general unsupervised clustering function **TSQ_us_cluster**, where more information about choices for clustering algorithms (for *ClusterMethRow* and *ClusterMethCol*) and the cells of parameters (for *ClusterParamsRow* and *ClusterParamsCol*) can be found.
Note that the output of this function is to local Matlab files; in the same way that `TSQ_normalize` takes in `HCTSA_loc` and outputs normalized versions as **HCTSA\_N**, `TSQ_cluster` takes in **HCTSA\_N** and writes output to a new file: **HCTSA\_cl**, containing clustered (re-ordered) versions of the data in **HCTSA\_N**.

### Grouping elements {#sec:grouping_variables}

Throughout our analysis, it is often important to incorporate grouped structure particularly between time series in a classification dataset, for example.
This is done using `TSQ_LabelGroups`.

## Visualizing {#sec:visualizing}

There are many tasks that involve understanding the rich structure
contained in data matrices by visualizing them. In this section we
describe some basic tools we have developed to visualize the behavior of
time series and operations in the data matrix.

### Visualizing the data matrix using `TSQ_plot_DataMatrix` {#sec:visDatamatrix}

    TSQ_plot_DataMatrix

[[mention how keywords can be used using the `TSQ_LabelGroups`]]
[[should include the data matrix visualization code, dimensionality
reduction code, and greedy feature selection code, as examples, each
with example workflows starting from retrieval from the database]]
[[mention how do similarity search, etc.]]