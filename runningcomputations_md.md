# Performing calculations using `TS_compute`

Once a time-series dataset and the operation library has been specified,  NaN entries in the corresponding `HCTSA_loc.mat`.

Calculations are performed using the function `TS_compute`, which stores results back into the matrices in `HCTSA_loc`.
This function can be run without inputs:

    % Compute all missing values in HCTSA_loc.mat:
    TS_compute;

Running `TS_compute` will begin running operations on time series in `HCTSA_loc.mat` for which elements in **TS\_DataMat** are **NaN**s (indicating that they have not been run before).
The results will be stored back in the matrices of `HCTSA_loc.mat`, i.e., **TS_DataMat** (output of each operation on each time series), **TS_CalcTime** (calculation time for each operation on each time series), and **TS_Quality** (labels indicating errors or special-valued outputs).

When all NULL entries in **TS_DataMat** have been calculated, **TS_compute** saves the results back to the local file: `HCTSA_loc.mat`.

## Custom settings for running `TS_compute`

By specifying the first input to 1 to calculate across available cores using the Matlab Parallel Processing Toolbox:

    % Compute all missing values in HCTSA_loc.mat using parallel processing:
    TS_compute(1);

You can also specify a custom range of ts_ids and op_ids to compute:

    % Compute missing values in HCTSA_loc.mat for ts_ids from 1:10 and op_ids from 1:1000
    TS_compute(0,1:10,1:1000);

Specify what values should be recomputed:

    % Compute all values that have never previous been calculated or previously returned an error:
    TS_compute(0,[],[],'error');

To run the same procedure on a custom file (that is not `HCTSA_loc.mat`), you can specify this also:

    % Compute all missing values in my_HCTSA_file.mat:
    TS_compute(0,[],[],'missing','my_HCTSA_file.mat',0);

By default, all computations will be displayed to screen (which is useful for error checking), but this functionality can be suppressed by setting the final input to zero:

    % Compute all missing values in HCTSA_loc.mat, suppressing output to screen:
    TS_compute(0,[],[],'missing','',0);
