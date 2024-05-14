# Performing calculations

An _hctsa_ dataset has been initialized (specifying details of a time-series dataset and operations to include using `TS_Init`), all results entries in the resulting `HCTSA.mat` are set to `NaN`, corresponding to results that are as yet uncomputed.

Calculations are performed using the function `TS_Compute`, which stores results back into the matrices in `HCTSA.mat`. This function can be run without inputs to compute all missing values in the default hctsa file, `HCTSA.mat`:

```
    % Compute all missing values in HCTSA.mat:
    TS_Compute();
```

`TS_Compute` will begin evaluating operations on time series in `HCTSA.mat` for which elements in `TS_DataMat` are `NaN` (i.e., computations that have not been run previously). Results are stored back in the matrices of `HCTSA.mat`: `TS_DataMat` (output of each operation on each time series), `TS_CalcTime` (calculation time for each operation on each time series), and `TS_Quality` (labels indicating errors or special-valued outputs).

## Custom settings for running `TS_Compute`

(1) Computing features in parallel across available cores using Matlab's Parallel Processing Toolbox. This can be achieved by setting the first input to `true`:

```
    % Compute all missing values in HCTSA.mat using parallel processing:
    TS_Compute(true);
```

(2) Computing across a custom range of time-series IDs (`ts_id`) and operation IDs (`op_id`). This can be achieved by setting the second and third inputs:

```
    % Compute missing values in HCTSA.mat for ts_ids from 1:10 and op_ids from 1:1000
    TS_Compute(false,1:10,1:1000);
```

(3) Specifying what types of values should be computed:

```
    % Compute all values that have never been computed before (default)
    TS_Compute(false,[],[],'missing');
    % Compute all values that have never previous been calculated OR have previously been computed but returned an error:
    TS_Compute(false,[],[],'error');
```

(4) Specifying a custom .mat file to operate on (`HCTSA.mat` is the default):

```
    % Compute all missing values in my_HCTSA_file.mat:
    TS_Compute(false,[],[],'missing','my_HCTSA_file.mat');
```

(5) Suppress commandline output. All computations are displayed to screen by default (which can be overwhelming but is useful for error checking). This functionality can be suppressed by setting the final (6th) input to `false`:

```
    % Compute all missing values in HCTSA.mat, suppressing output to screen:
    TS_Compute(false,[],[],'missing','',false);
```

## Computation approaches for full datasets

Computing features for full time-series datasets can be time consuming, especially for large datasets of long time series. An appropriate strategy therefore depends on the time-series length, the number of time series in the dataset, and the computational resources available. When multiple cores are available, it is always recommended to use the parallel setting (i.e., as `TS_Compute(true)`).

### Computation time scaling

The first thing to think about is how the time taken to compute 7749 features of v0.93 of _hctsa_ scales with the length of time series in your dataset (see plot below). The figure compares results using a single core (e.g., `TS_Compute(false)`) to results using a 16-core machine, with parallelization enabled (e.g., `TS_Compute(true)`).

![](../../.gitbook/assets/computeScaling.png)

Times may vary across on individual machines, but the above plot can be used to estimate the computation time per time series, and thus help decide on an appropriate computation strategy for a given dataset.

Note that if computation times are too long for the computational resources at hand, one can always choose a reduced set of features, rather than the full set of >7000, to get a preliminary understanding of the dataset. One such reduced set of features is in `INP_ops_reduced.txt`. We plan to reduced additional reduced feature sets, determined according to different criteria, in future.

### On a single machine

If only a single machine is available for computation, there are a couple of options:

1. For small datasets, when it is feasible to run all computations in a single go, it is easiest to run computations within Matlab in a single call of `TS_Compute`.
2. For larger datasets that may run for a long time on a single machine, one may wish to use something like the provided `sample_runscript_matlab` script, where `TS_Compute` commands are run in a loop over time series, compute small sections of the dataset at a time (and then saving the results to file, e.g., `HCTSA.mat`), eventually covering the full dataset iteratively.

### On a distributed compute cluster using Matlab

Code for running distributed _hctsa_ computations on a cluster (using pbs or slurm schedulers) is [here](https://github.com/benfulcher/distributed\_hctsa). The strategy is as follows: with a distributed computing setup, a local Matlab file (`HCTSA.mat`) can be split into smaller pieces using `TS_Subset`, which outputs a new data file for a particular subset of your data, e.g., `TS_Subset('raw',1:100)` will generate a new file, `HCTSA_subset.mat` that contains just time series with IDs from 1 to 100. Computing features for time series in each such subset can then be run on a distributed computing setup. For example, with a different compute node computing a different subset (by queuing batch jobs that each work on a given subset of time series). After all subsets have been computed, the results are recombined into a single `HCTSA.mat` file using `TS_Combine` commands.

### Using mySQL to facilitate distributed computing

Distributing feature computations on a large-scale distributed computing setup can be better suited to a linked mySQL database, especially for datasets that grow with time, as new time series can be easily added to the database. In this case, computation proceeds similarly to above, where shell scripts on a distributed cluster computing environment can be used to distribute jobs across cores, with all individual jobs writing to a centralized _mySQL_ server. A set of Matlab code that generates an appropriately formatted mySQL database and interfaces with the database to facilitate _hctsa_ feature computation is included with the software package, and is described in detail [here](../overview\_mysql\_database/).
