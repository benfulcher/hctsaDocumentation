# An example analysis using *hctsa* in Matlab

<!--## Overview of an analysis-->

The basic sequence of a Matlab-based hctsa analysis is to:
1. Initialize a `HCTSA_loc.mat` file, which contains all of the information about the set of time series and operations in your analysis, and stores the results of applying all operations to all time series (using the `TS_init` function described below),
2. After initializing an `HCTSA_loc.mat` file, [computing all operations on all time series](calculating.md) is done using `TS_compute`, which stores the results back to this local file. The results are structured in local Matlab files containing matrices (that store the results of the computations) and structure arrays (that store information about the time-series data and operations), as described [here](hctsa_structure.md).
3. After the computation is complete, [a range of processing, analysis, and plotting functions](analyzing_visualizing.md) are also provided with the software.

More detail on the initialization step is provided below:

## Specifying a set of time series and operations using `TS_init`

As described in the [overview section](setup.md), initiating a dataset for an hctsa analysis involves specifying an input file for each of:
1. the time series to analyze (`INP_ts.mat` or `INP_ts.txt`).
2. the code to run (`INP_mops.txt`).
3. the features to extract from that code (`INP_ops.txt`).

Details of how to format these input files are [here](input_files.md).

To use the default library of operations, you can initiate a time-series dataset using the following:

    TS_init('INP_test_ts.mat');

All sets can be specified as follows:

    TS_init('INP_ts.mat','INP_mops.txt','INP_ops.txt');

`TS_init` produces a Matlab file, `HCTSA_loc.mat`, containing all of the structures required to understand the set of time series, operations, and the results of their computation (explained [here](hctsa_structure.md)).
Note that if only the first input file is provided, the default *hctsa* library of operations will be used.

Through this initialization process, each time series will be assigned a unique ID, as will each master operation, and each operation (which can be reassigned using `TS_ReIndex`).