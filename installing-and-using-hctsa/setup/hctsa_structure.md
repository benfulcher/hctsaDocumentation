# Structure of the hctsa framework

## Overview

The _hctsa_ framework consists of three basic objects containing relevant metadata:

1. _Master Operations_ specify pieces of code \(Matlab functions\) and their inputs to be computed. Taking in a single time series, master operations can generate a large number of outputs as a Matlab structure, each of which can be identified with a single _operation_ \(or 'feature'\).
2. _Operations_ \(or 'features'\) are a single number summarizing some measure of structure in a time series. In _hctsa_, each operation links to an output from a piece of evaluated code \(a _master operation_\).
3. _Time series_ are univariate and uniformly sampled time-ordered measurements.

These three different objects are summarized below:

|  | **Master Operation** | **Operation** | **Time Series** |
| :---: | :---: | :---: | :--- |
| **Summary**: | Code and inputs to execute | Single feature | Univariate data |
| **Example**: | `CO_AutoCorr(x,1:5,'TimeDomain')` | `AC_1` | \[1.2, 33.7, -0.1, ...\] |

In the example above, a _master operation_ specifies the code to run, `CO_AutoCorr(x,1:5,'TimeDomain')`, which outputs the autocorrelation of the input time series \(`x`\) at lags 1, 2, ..., 5.
Each operation \(or 'feature'\) is a single number that draws on this set of outputs, for example, the autocorrelation at lag 1, which is named `AC_1`.
(This example is illustrative: the default library computes each autocorrelation with its own master operation, e.g., `CO_AutoCorr(x_z,1,'Fourier')` for `AC_1`, where `x_z` is the z-scored time series.)

In the _hctsa_ framework, master operations, operations, and time series are stored as **tables** that contain all of their associated keywords and metadata \(and actual time-series data in the case of time series\).

For a given _hctsa_ analysis, the user must specify a set of code to evaluate \(_master operations_\), their associated individual outputs to measure \(_operations_\), and a set of time series to evaluate the features on \(_time series_\).

We provide a default library of 6392 _operations_ \(derived from 927 unique _master operations_\), specified in the input files `INP_ops_hctsa.txt` and `INP_mops_hctsa.txt` \(in the `FeatureSets` directory\).
This can be customized, and additional pieces of code can also be added to the repository.

### The results of a _hctsa_ analysis

Having specified a set of master operations, operations, and time series, the results of computing these functions in the time series data are stored in three matrices:

* **TS\_DataMat** is an _n_ x _m_ data matrix containing the results of applying _m_ operations to the _n_ time series.
* **TS\_Quality** is an _n_ x _m_ matrix containing quality labels for each operation output \(coding different outputs such as errors or NaNs\). Quality labels are described in the section below.
* **TS\_CalcTime** is an _n_ x _m_ matrix containing calculation times for each operation output. Note that the calculation time stored is for the corresponding master operation.

When a dataset is initialized \(using `TS_Init`\), all three matrices are filled with `NaN`, indicating values that have not yet been computed; `TS_Compute` then fills them in.

### HCTSA .mat files

Each `HCTSA*.mat` file includes the tables described above: for **TimeSeries** \(corresponding to the rows of the **TS\_** matrices\), **Operations** \(corresponding to columns of the **TS\_** matrices\), and **MasterOperations**, corresponding to the code evaluated to compute the operations.
In addition, the results are stored as above: **TS\_DataMat**, **TS\_Quality**, and **TS\_CalcTime**.
`TS_Init` also stores **gitInfo**, a record of the version of the _hctsa_ code used to initialize the file.

## Quality labels

_Quality labels_ are used to indicate when operations take non-real values, or when fatal errors were encountered. Quality labels are stored in local Matlab files as the **TS\_Quality** matrix.

When the quality label is nonzero, this indicates that a _special-valued output_ occurred.
In this case, the value stored in **TS\_DataMat** is `NaN`, and the quality label codes the special output value.
\(Files computed with older versions of _hctsa_ stored these values as zero; `TS_LoadData` converts both to `NaN` when loading.\)
A quality label of `NaN` indicates a value that has not yet been computed.

| **Quality label** | **Description** |
| :---: | :---: |
| 0 | No problems with calculation. Output was a real number. |
| 1 | A fatal error was encountered. |
| 2 | Output of the code was **NaN**. |
| 3 | Output of the code was **Inf**. |
| 4 | Output of the code was **-Inf** |
| 5 | Output had a non-zero imaginary component |
| 6 | Output was empty \(e.g., `[]`\) |
| 7 | Field specified for this operation did not exist in the master operation output structure |

