# An example analysis using *hctsa* in Matlab

<!--## Overview of an analysis-->

A basic sequence of a Matlab-based analysis is to:
1. Initialize a **HCTSA_loc.mat** file, which contains all of the information about the set of time series and operations in your analysis, and stores the results of applying all operations to all time series (using the `TS_init` function, e.g., for an input file specifying a test dataset: `TS_init('INP_test_ts.mat');`),
2. After initializing an **HCTSA_loc.mat** file, [computing all operations on all time series](running_computations.md) is done using `TS_compute`, which stores the results back to this local file. The results are structured in local Matlab files containing matrices (that store the results of the computations) and structure arrays (that store information about the time-series data and operations), as described [here](hctsa_structure.md).
3. After the computation is complete, [a range of processing, analysis, and plotting functions](analyzing_visualizing.md) are also provided with the software.

More detail on these steps is provided below:

## Specifying a set of time series and operations using `TS_init`

As described in the [overview section](setup.md), initiating a dataset involves specifying an input file for each of:
1. the time series to analyze (`INP_ts.mat` or `INP_ts.txt`).
1. the code to run (`INP_mops.txt`).
1. the features to extract from that code (`INP_ops.txt`).

This is achieved using, for example:

    TS_init('INP_ts.mat','INP_mops.txt','INP_ops.txt')

Details of how to format these input files are [here](input_files.md).
Note that if only the first input file is provided, the default *hctsa* library of operations will be used.

Note that through this initialization process, each time series will be assigned a unique ID, as will each master operation, and each operation.

## Computing using `TS_compute`

Computations can then be run on the dataset using `TS_compute`, which is described [here](calculating.md).

Note that you want to use a distributed set up (which is better suited for a mySQL database), you can run computations across different nodes by splitting the Matlab file into smaller pieces using `TS_subset`, which outputs a new data file for a particular subset of your data, e.g.,
`TS_subset('loc',1:100)` will generate a new file, **HCTSA_loc_subset.mat** that contains just TimeSeries with IDs from 1 to 100.

## Analyzing

Once all computations are complete, analysis can proceed using a suite of analysis and visualization functions described [here](analyzing_visualizing.md).
