# Working with *hctsa* files

When running hctsa analyses, often you want to take subsets of time series (to look in more detail at a subset of your data) or features from an hctsa analysis (to explore the behavior of different feature subsets), or combine multiple subsets of data together (e.g., as you obtain more data).
The *hctsa* package contains a range of functions for these types of tasks, by working directly with hctsa .mat files, and are described below (for working with an hctsa database see [here](overview_mysql_database.md)).

## Retrieving time series (or operations) of interest by matching on assigned keywords

Many time-series classification problems involve filtering subsets of time series based on keyword matching, where keywords are specified in the [input file](input_files.md) provided when initializing a dataset.

Most filtering functions (such as those listed in this section), require you to specify a range of IDs of TimeSeries or Operations in order to specify them.
Recall that each TimeSeries and Operation is assigned a unique ID (assed as the ID field in the corresponding structure array).
To quickly get the IDs of time series that match a given keyword, the following function can be used:

    matchingIDs = TS_getIDs(theKeyword,'HCTSA_N.mat');

These IDs can then be used in the functions below (e.g., to clear data, or extract a subset of data).

## Clearing or removing data from an hctsa dataset using `TS_local_clear_remove`

Sometimes you may want to remove a time series from an *hctsa* dataset because the data was not properly processed, for example.
Or one operation may have produced errors because of a missing toolbox reference, or you may have altered the code for an operation, and want to clear the stored results from previous calculations.

The function `TS_local_clear_remove` achieves these tasks when working directly with .mat files (NB: if using a mySQL database, [`SQL_clear_remove`](clearing_or_removing_data.md) should be used instead).

It loads in a an hctsa .mat data file, clears or removes the specified time series or operations, and then writes the result back to the file.

Example usage, to remove time series with IDs 1:5 from HCTSA_loc.mat:

    TS_local_clear_remove('ts',1:5,1,'HCTSA_loc.mat');

See the documentation in the function file for details of the inputs.

## Extracting a subset from an hctsa dataset using `TS_subset`

Sometimes it's useful to retrieve a subset of an hctsa dataset, when analyzing just a particular class of time series, for example, or investigating a balanced subset of data for time-series classification, or to compare the behavior of a reduced subset of features.
This can be done with `TS_subset`, which takes in a hctsa dataset and generates the desired subset, which can be saved to a new .mat file.

Example usage is:

    TS_subset('norm',1:100,[],1,'HCTSA_subset.mat')

This imports data from 'HCTSA_N.mat' (specifying 'norm'), then creates a new dataset containing only time series with IDs in the range 1--100, and all operations, saving the result back to 'HCTSA_subset.mat' (see documentation for all inputs).

## Combining multiple hctsa datasets using `TS_combine`

Note that 
