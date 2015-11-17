# Performing calculations using `TS_compute`

Once a time-series dataset and the operation library has been specified, and an *hctsa* dataset initialized (using `TS_init`), all results entries in the resulting `HCTSA.mat` are set to `NaN`, corresponding to results that are as yet uncomputed.

Calculations are performed using the function `TS_compute`, which stores results back into the matrices in `HCTSA.mat`.
This function can be run without inputs to compute all missing values in the default hctsa file, `HCTSA.mat`:

    % Compute all missing values in HCTSA.mat:
    TS_compute;

Running `TS_compute` will begin running operations on time series in `HCTSA.mat` for which elements in **TS\_DataMat** are **NaN**s (indicating that they have not been run before).
Results will be stored back in the matrices of `HCTSA.mat`, i.e., **TS_DataMat** (output of each operation on each time series), **TS_CalcTime** (calculation time for each operation on each time series), and **TS_Quality** (labels indicating errors or special-valued outputs).

## Custom settings for running `TS_compute`

By specifying the first input to 1 to calculate across available cores using the Matlab Parallel Processing Toolbox:

    % Compute all missing values in HCTSA.mat using parallel processing:
    TS_compute(1);

You can also specify a custom range of ts_ids and op_ids to compute:

    % Compute missing values in HCTSA.mat for ts_ids from 1:10 and op_ids from 1:1000
    TS_compute(0,1:10,1:1000);

Specify what values should be recomputed:

    % Compute all values that have never previous been calculated or previously returned an error:
    TS_compute(0,[],[],'error');

To run the same procedure on a custom file (that is not `HCTSA.mat`), you can specify this also:

    % Compute all missing values in my_HCTSA_file.mat:
    TS_compute(0,[],[],'missing','my_HCTSA_file.mat',0);

By default, all computations will be displayed to screen (which is useful for error checking), but this functionality can be suppressed by setting the final input to zero:

    % Compute all missing values in HCTSA.mat, suppressing output to screen:
    TS_compute(0,[],[],'missing','',0);

### Computing a full dataset

For larger datasets, it is sensible to compute small sections of the dataset at a time (and save the results to file), eventually covering the full dataset iteratively.
We have provided a sample runscript for this purpose as `sample_runscript_matlab`, which allows the user to specify the increment at which results are saved back to `HCTSA.mat` (or any custom file).

## Distributing computations

Distributing computations across multiple computers on a large scale is better suited to [a linked a mySQL database](overview_mysql_database.md)), however, this can also be achieved in a basic way within Matlab.
A local Matlab file (`HCTSA.mat`) can be split into smaller pieces using `TS_subset`, which outputs a new data file for a particular subset of your data, e.g.,
`TS_subset('loc',1:100)` will generate a new file, **HCTSA_subset.mat** that contains just TimeSeries with IDs from 1 to 100.
Each such subset can then be run on a different computer, and the results later recombined into a single HCTSA file using `TS_combine`.
