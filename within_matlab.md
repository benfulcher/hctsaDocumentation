# An example analysis workflow using *hctsa* in Matlab

At its core, *hctsa* analysis involves computing a library of time-series analysis features (which we call *operations*) on a time-series dataset.
<!--## Overview of an analysis-->

The basic sequence of a Matlab-based *hctsa* analysis is to:
1. *Initialize* a `HCTSA.mat` file, which contains all of the information about the set of time series and operations in your analysis, as well as the results of applying all operations to all time series, using [`TS_init`](input_files.md),

2. These operations can be computed on your time-series data using [`TS_compute`](calculating.md). The results are structured in the local `HCTSA.mat` file containing matrices (that store the results of the computations) and structure arrays (that store information about the time-series data and operations), as described [here](hctsa_structure.md).

3. After the computation is complete, [a range of processing, analysis, and plotting functions](analyzing_visualizing.md) are provided to understand and interpret the results.

More detail on the initialization step (1) is provided below:
