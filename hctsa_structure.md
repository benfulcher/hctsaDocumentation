# Structure of the _hctsa_ package

## Overview

The _hctsa_ framework consists of three basic objects containing relevant metadata:

1. *Master Operations* specify pieces of code (Matlab functions) and their inputs to be computed. Taking in a single time series, master operations can generate a large number of outputs as a Matlab structure, each of which can be identified with a single *operation* (or 'feature').
2. *Operations* (or 'features') are a single number summarizing some measure of structure in a time series. In _hctsa_, each operation links to an output from a piece of evaluated code (a *master operation*).
3. *Time series* are univariate and uniformly sampled time-ordered measurements.

These three different objects are summarized below:

| | **Master Operation** | **Operation** | **Time Series** |
|:-------------:|:-------------:|:-------------:|
| **Summary**: | Code and inputs to execute | Single feature | Univariate data|
| **Example**: | `CO_AutoCorr(x,1:5,'TimeDomain')` | `AC_1` | [1.2, 33.7, -0.1, ...] |

In the example above, a *master operation* specifies the code to run, `CO_AutoCorr(x,1:5,'TimeDomain')`, which outputs the autocorrelation of the input time series (`x`) at lags 1, 2, ..., 5.
Each operation (or 'feature') is a single number that draws on this set of outputs, for example, the autocorrelation at lag 1, which is named `AC_1`, for example.

In the _hctsa_ framework, master operations, operations, and time series are stored as **tables** that contain all of their associated keywords and metadata (and actual time-series data in the case of time series).

For a given _hctsa_ analysis, the user must specify a set of code to evaluate (*master operations*), their associated individual outputs to measure (*operations*), and a set of time series to evaluate the features on (*time series*).

We provide a default library of over 7700 *operations* (derived from approximately 1000 unique *master operations*).
This can be customized, and additional pieces of code can also be added to the repository.

### The results of a _hctsa_ analysis
Having specified a set of master operations, operations, and time series, the results of computing these functions in the time series data are stored in three matrices:

-   **TS_DataMat** is an *n* x *m* data matrix containing the results of applying *m* operations to the *n* time series.

-   **TS_Quality** is an *n* x *m* matrix containing quality labels for each operation output (coding different outputs such as errors or NaNs).
Quality labels are described in the section below.

-   **TS_CalcTime** is an *n* x *m* matrix containing calculation times for each operation output. Note that the calculation time stored is for the corresponding master operation.

### HCTSA .mat files
Each `HCTSA*.mat` file includes the tables described above: for **TimeSeries** (corresponding to the rows of the **TS_** matrices), **Operations** (corresponding to columns of the **TS_** matrices), and **MasterOperations**, corresponding to the code evaluated to compute the operations.
In addition, the results are stored as above: **TS_DataMat**, **TS_Quality**, and **TS_CalcTime**.

## Quality labels

*Quality labels* are used to indicate when operations take non-real values, or when fatal errors were encountered.
Quality labels are stored in the **Quality** column of the **Results** table in the *mySQL* database, and in local Matlab files as the **TS_Quality** matrix.

When the quality label is nonzero, this indicates that a *special-valued output* occurred.
In this case, the output value of the operation is set to zero, as a convention, and the quality label codes the special output value:

| **Quality label** | **Description** |
|:-------------:|:-------------:|
| 0 | No problems with calculation. Output was a real number. |
| 1 | A fatal error was encountered. |
| 2 | Output of the code was **NaN**.|
| 3 | Output of the code was **Inf**. |
| 4 | Output of the code was **-Inf** |
| 5 | Output had a non-zero imaginary component |
| 6 | Output was empty (e.g., `[]`) |
| 7 | Field specified for this operation did not exist in the master operation output structure |
