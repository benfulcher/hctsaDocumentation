# Performing calculations using `TS_compute`

Once a time-series dataset and the operation library has been specified, and an *hctsa* dataset initialized (using `TS_init`), all results entries in the resulting `HCTSA.mat` are set to `NaN`, corresponding to results that are as yet uncomputed.

Calculations are performed using the function `TS_compute`, which stores results back into the matrices in `HCTSA.mat`.
This function can be run without inputs to compute all missing values in the default hctsa file, `HCTSA.mat`:
```matlab
    % Compute all missing values in HCTSA.mat:
    TS_compute;
```
Running `TS_compute` will begin running operations on time series in `HCTSA.mat` for which elements in **TS\_DataMat** are **NaN**s (indicating that they have not been run before).
Results will be stored back in the matrices of `HCTSA.mat`, i.e., **TS_DataMat** (output of each operation on each time series), **TS_CalcTime** (calculation time for each operation on each time series), and **TS_Quality** (labels indicating errors or special-valued outputs).

## Custom settings for running `TS_compute`

By specifying the first input to 1 to calculate across available cores using the Matlab Parallel Processing Toolbox:
```matlab
    % Compute all missing values in HCTSA.mat using parallel processing:
    TS_compute(true);
```
You can also specify a custom range of ts_ids and op_ids to compute:
```matlab
    % Compute missing values in HCTSA.mat for ts_ids from 1:10 and op_ids from 1:1000
    TS_compute(0,1:10,1:1000);
```
Specify what values should be recomputed:
```
    % Compute all values that have never previous been calculated or previously returned an error:
    TS_compute(0,[],[],'error');
```
To run the same procedure on a custom file (that is not `HCTSA.mat`), you can specify this also:
```matlab
    % Compute all missing values in my_HCTSA_file.mat:
    TS_compute(0,[],[],'missing','my_HCTSA_file.mat',0);
```
By default, all computations will be displayed to screen (which is useful for error checking), but this functionality can be suppressed by setting the final input to zero:
```matlab
    % Compute all missing values in HCTSA.mat, suppressing output to screen:
    TS_compute(0,[],[],'missing','',0);
```

## Computation approaches for full datasets

The optimal approach for computing features for full time-series datasets depends on the time-series length, the number of time series in the dataset, and the computational resources available.
When multiple cores are available, it is always recommended to use the parallel setting (i.e., as `TS_compute(true)`).

### Computation time scaling

The first thing to think about is how the time taken to compute 7749 features of v0.93 of _hctsa_ scales with the length of time series in your dataset (see plot below). The figure compares results using a single core (e.g., `TS_compute(false)`) to results using a 16-core machine, with parallelization enabled (e.g., `TS_compute(true)`).

![](/assets/computeScaling.png)

Times may vary across on individual machines, but the above can be used as an estimate of the computation time per time series that can motivate the correct computation strategy for a given dataset.

Note that if computation times are too long for the resources at hand, one can always choose a reduced set of features, rather than the full set of >7000, to get a preliminary understanding of their data.

### On a single machine
If only a single machine is available for computation, there are a couple of options:

1. For small datasets, when it is feasible to run all computations in a single go, it is easiest to run computations within Matlab in a single call of `TS_compute`.
2. For larger datasets that may run for a long time on a single machine, one may wish to use something like the provided `sample_runscript_matlab` script, where `TS_compute` commands are run in a loop over time series, compute small sections of the dataset at a time (and then saving the results to file, e.g., `HCTSA.mat`), eventually covering the full dataset iteratively.

### On a distributed compute cluster (using Matlab)

With a distributed computing setup, a local Matlab file (`HCTSA.mat`) can be split into smaller pieces using `TS_subset`, which outputs a new data file for a particular subset of your data, e.g., `TS_subset('raw',1:100)` will generate a new file, **HCTSA_subset.mat** that contains just TimeSeries with IDs from 1 to 100.
Computing features for time series in each such subset can then be run on a different compute node (by queuing batch jobs that each work on a given subset of time seires), and the results later recombined into a single `HCTSA.mat` file using `TS_combine` commands, as described [here](working_with_hctsa_files.md).

### Using mySQL to facilitate distributed computing

Distributing computations across multiple computers on a large scale is better suited to [a linked a mySQL database](overview_mysql_database.md), which also best accomodates datasets that grow with time, as new time series can be easily added to the database.
In this case, computation proceeds similarly to above, on a cluster, where shell scripts can be used to distribute jobs across cores, that all write to a centralized _mySQL_ server.
Indeed, any machine can easily be used to contribute results, by assigning them different subsets of time series IDs to compute and write to the server.
Full details for how to implement this are [here](overview_mysql_database.md).