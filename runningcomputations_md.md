# Performing calculations using `TS_compute`

Once a time-series dataset and the operation library has been specified,  NaN entries in the corresponding `HCTSA_loc.mat`.

Calculations are performed using the function `TS_compute`, which stores results back into the matrices in `HCTSA_loc`.
This function can be run without inputs:

    % Compute missing values in HCTSA_loc.mat:
    TS_compute;

Or, by specifying the first input to 1 to calculate across available cores using the Matlab Parallel Processing Toolbox:

    % Compute missing values in HCTSA_loc.mat using parallel processing:
    TS_compute(1);

By default, all computations will be displayed to screen (which is useful for error checking), but this functionality can be suppressed by setting the third input to zero.

Running `TS_compute` will begin running operations on time series in `HCTSA_loc.mat` for which elements in **TS\_DataMat** are **NaN**s (indicating that they have not been run before) or have a [quality label](retrieving_to_compute.md) of 1 (indicating a prior error).
The results will be stored back in the matrices of `HCTSA_loc.mat`, i.e., **TS\_DataMat** (output of each operation on each time series), **TS\_CalcTime** (calculation time for each operation on each time series), and **TS\_Quality** (labels indicating errors or special-valued outputs).

When all NULL entries in **TS\_DataMat** have been calculated, **TS_compute** saves the results back to the local file: `HCTSA_loc.mat`.