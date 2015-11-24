# Working with *hctsa* files

When running *hctsa* analyses, often you want to take subsets of time series (to look in more detail at a subset of your data) or subsets of operations (to explore the behavior of different feature subsets), or combine multiple subsets of data together (e.g., as additional data arrive).

The *hctsa* package contains a range of functions for these types of tasks, working directly with *hctsa* .mat files, and are described below.
Note that these types of tasks are easier to manage when *hctsa* data are stored in a [mySQL database](overview_mysql_database.md).

## Retrieving time series (or operations) of interest by matching on assigned keywords using `TS_getIDs`

Many time-series classification problems involve filtering subsets of time series based on keyword matching, where keywords are specified in the [input file](input_files.md) provided when initializing a dataset.

Most filtering functions (such as those listed in this section), require you to specify a range of IDs of TimeSeries or Operations in order to specify them.
Recall that each TimeSeries and Operation is assigned a unique ID (assed as the ID field in the corresponding structure array).
To quickly get the IDs of time series that match a given keyword, the following function can be used:

    TimeSeriesIDs = TS_getIDs(theKeyword,'HCTSA_N.mat');

Or the IDs of operations tagged with the 'entropy' keyword:

    OperationIDs = TS_getIDs('entropy','norm','ops');

These IDs can then be used in the functions below (e.g., to clear data, or extract a subset of data).

Note that to get a quick impression of the unique time-series keywords present in a dataset, use the function `TS_WhatKeywords`, which gives a text summary of the unique keywords in an *hctsa* dataset.

## Clearing or removing data from an *hctsa* dataset using `TS_local_clear_remove`

Sometimes you may want to remove a time series from an *hctsa* dataset because the data was not properly processed, for example.
Or one operation may have produced errors because of a missing toolbox reference, or you may have altered the code for an operation, and want to clear the stored results from previous calculations.

For example, often you want to remove from your operation library operations that are dependent on the location of the data (e.g., its mean: `'locdep'`), that only operate on positive-only time series (`'posOnly'`), that require the TISEAN package (`'tisean'`), or that are stochastic (i.e., they give different results when repeated, `'stochastic'`).

The function `TS_local_clear_remove` achieves these tasks when working directly with .mat files (NB: if using a mySQL database, [`SQL_clear_remove`](clearing_or_removing_data.md) should be used instead).

`TS_local_clear_remove` loads in a an *hctsa* .mat data file, clears or removes the specified time series or operations, and then writes the result back to the file.

*Example 1*: Clear all computed data from time series with IDs 1:5 from `HCTSA.mat` (specifying `'raw'`):

    TS_local_clear_remove('ts',1:5,0,'raw');

*Example 2*: Remove all operations with the keyword 'tisean' (that depend on the [TISEAN package](http://www.mpipks-dresden.mpg.de/~tisean/Tisean_3.0.1/index.html)) from `HCTSA.mat`:

    TS_local_clear_remove('ops',TS_getIDs('tisean','raw','ops'),1,'raw');

*Example 3*: Remove all operations that require positive-only data (the `'posOnly'` keyword) from `HCTSA.mat`:

    TS_local_clear_remove('ops',TS_getIDs('posOnly','raw','ops'),1,'raw');

*Example 4*: Remove all operations that are location dependent (the `'locdep'` keyword) from `HCTSA.mat`:

    TS_local_clear_remove('ops',TS_getIDs('locdep','raw','ops'),1,'raw');

See the documentation in the function file for additional details about the inputs to `TS_local_clear_remove`.

## Extracting a subset from an *hctsa* dataset using `TS_subset`

Sometimes it's useful to retrieve a subset of an *hctsa* dataset, when analyzing just a particular class of time series, for example, or investigating a balanced subset of data for time-series classification, or to compare the behavior of a reduced subset of features.
This can be done with `TS_subset`, which takes in a *hctsa* dataset and generates the desired subset, which can be saved to a new .mat file.

*Example 1*: Import data from 'HCTSA_N.mat', then save a new dataset containing only time series with IDs in the range 1--100, and all operations, to 'HCTSA_N_subset.mat' (see documentation for all inputs).

    TS_subset('norm',1:100,[],1,'HCTSA_N_subset.mat')

Note that the subset in this case will have be normalized using the full dataset of all time series, and just this subset (with IDs up to 100) are now being analyzed.
Depending on the normalization method used, different results would be obtained if the subsetting was performed prior to normalization.

*Example 2*: From `HCTSA.mat` (`'raw'`), save a subset of that dataset to 'HCTSA_healthy.mat' containing only time series tagged with the 'healthy' keyword:

    TS_subset('raw',TS_getIDs('healthy','raw'),[],1,'HCTSA_healthy.mat')

## Combining multiple *hctsa* datasets using `TS_combine`

When analyzing a growing dataset, sometimes new data needs to be combined with computations on existing data.
Alternatively, when computing a large dataset, sometimes you may wish to compute sections of it separately, and may later want to combine each section into a full dataset.

To combine *hctsa* data files, you can use the `TS_combine` function.

*Example*: combine *hctsa* datasets stored in the files `HCTSA_healthy.mat` and `HCTSA_disease.mat` into a new combined file, `HCTSA_combined.mat`:

    TS_combine('HCTSA_healthy.mat','HCTSA_disease.mat',0,'HCTSA_combined.mat')

The third input, `compare_tsids`, controls the behavior of the function in combining time series.
By setting this to 1, `TS_combine` assumes that the TimeSeries IDs are comparable between the datasets (e.g., most common when using a [*mySQL* database to store *hctsa* data](overview_mysql_database.md)), and thus filters out duplicates so that the resulting *hctsa* dataset contains a unique set of time series.
By setting this to 0 (default), the output will contain a union of time series present in each of the two *hctsa* datasets.
In the case that duplicate TimeSeries IDs exist in the combination file, a new index will be generated in the combined file (where IDs assigned to time series are re-assigned as unique integers using `TS_ReIndex`).

In combining operations, this function works differently when data have been stored in a unified [*mySQL* database](overview_mysql_database.md), in which case operation IDs can be compared meaningfully and combined as an intersection.
However, when *hctsa* datasets have been generated using `TS_init`, the function will check that the same set of operations have been used in both files.
