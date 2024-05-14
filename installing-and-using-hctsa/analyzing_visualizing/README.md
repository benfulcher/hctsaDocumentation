# Analyzing and visualizing results

Once a set of operations have been computed on a time-series dataset, the results are stored in a local **HCTSA.mat** file. The result can be used to perform a wide variety of highly comparative analyses, such as those outlined in [our paper](http://rsif.royalsocietypublishing.org/content/10/83/20130048.full).

The type of analysis employed should the be motivated by the specific time-series analysis task at hand. Setting up the problem, guiding the methodology, and interpreting the results requires strong scientific input that should draw on domain knowledge, including the questions asked of the data, experience performing data analysis, and statistical considerations.

The first main component of an _hctsa_ analysis involves filtering and normalizing the data using `TS_Normalize`, described [here](filtering_and_normalizing.md), which produces a file called **HCTSA\_N.mat**. Information about the similarity of pairs of time series and operations can be computed using `TS_Cluster`, described [here](clustering_rows_and_columns.md) which stores this information in **HCTSA\_N.mat**. The suite of plotting and analysis tools that we provide with _hctsa_ work with this normalized data, stored in **HCTSA\_N.mat**, by default.

## Plotting and analysis functions:

* Visualizing structure in the data matrix using [`TS_PlotDataMatrix`](visualizing_the_data_matrix.md).
* Visualizing the time-series traces using [`TS_PlotTimeSeries`](plotting_the_time_series.md).
* Visualizing low-dimensional structure in the data using [`TS_PlotLowDim`](low_dim.md).
* Exploring similar matches to a target time series using [`TS_SimSearch`](sim_search.md).
* Visualizing the behavior of a given operation across the time-series dataset using [`TS_FeatureSummary`](feature_summary.md).

## Tools for classification tasks:

For time-series classification tasks, groups of time series can be labeled using the `TS_LabelGroups` function described [here](grouping.md); this group label information is stored in the local **HCTSA\*.mat** file, and used by default in the various plotting and analysis functions provided. Additional analysis functions are provided for basic time-series classification tasks:

* Explore the classification performance of the full library of features using [`TS_Classify`](ts_classify.md)
* Determine the features that \(individually\) best distinguish between the labeled groups using [`TS_TopFeatures`](ts_topfeatures.md)

