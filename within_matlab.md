# Running *hctsa* within Matlab

For small-scale, or non-distributed *hctsa* computations, the software can be run completely within Matlab (without linked to an external database).

This is much simpler than setting up and using a *mySQL* database, and avoids the overheads of reading and writing large quantities of data between Matlab and a database.

## Initiating using `TS_init`

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
