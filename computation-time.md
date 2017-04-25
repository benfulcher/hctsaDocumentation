# Computation approaches

The scaling of the time taken to compute 7749 features of v0.93 of _hctsa_ is shown below. The figure compares results using a single core (e.g., `TS_compute(false)`) to results using a 16-core machine, with parallelization enabled (e.g., `TS_compute(true)`).

![](/assets/computeScaling.png)

## Computing approaches
Depending on the size of the dataset, whether it may grow in the future, and the computational resources available, a different computing approach may be selected. When multiple cores are available, it is always recommended to use the parallel setting (i.e., as `TS_compute(true)`).
Note that if computation times are too long for the resources at hand, one can always choose a reduced set of features, rather than the full set of >7000, to get a preliminary understanding of their data.

### On a single machine
If only a single machine is available for computation, there are a couple of options:

1. For small datasets, when it is feasible to run all computations in a single go, it is easiest to run computations within Matlab in a single call of `TS_compute`.
2. For larger datasets that may run for a long time on a single machine, one may wish to use something like the provided `sample_runscript_matlab` script, where `TS_compute` commands are run in a loop over time series, saving results back to file (`HCTSA.mat`) at specified increments.

## On a distributed cluster (using Matlab)
On a cluster, shell scripts can be set up to queue batch jobs, where each batch job loops across a set of time series.
For example,
```matlab
TS_subset('../HCTSA.mat',tsid_min:tsid_max,[],1,customFile);
```