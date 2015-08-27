# Structure of *hctsa*

## Overview

As explained [above](database_structure.md), the *mySQL* database is structured into 3 parts:

1. *Master Operations* specify pieces of code (Matlab functions) and their inputs to be computed. Taking in a time series, master operations often generate a large number of outputs, each of which can be identified with an *operation* (or *feature*).
2. *Operations* (or *features*) are a single number summarizing some measure of structure in a time series. In *hctsa*, each operation links to an output from a piece of evaluated code (a *master operation*).
3. *Time series* are univariate, uniformly sampled, time-ordered measurements. The data, and keyword labels for each time series are stored in the database.

These three different objects are summarized below:

| | **Master Operation** | **Operation** | **Time Series** |
|:-------------:|:-------------:|:-------------:|
| **Summary**: | Code and inputs to execute | Single feature | Univariate data|
| **Example**: | `CO_AutoCorr(x,1:5,'TimeDomain')` | `AC_1` | [1.2, 33.7, -0.1, ...] |

In the example above, a *master operation* specifies the code to run, `CO_AutoCorr(x,1:5,'TimeDomain')`, which outputs the autocorrelation of the input time series (*x*) at lags 1, 2, ..., 5.
Each operation is a single number that draws on this set of outputs, for example, the autocorrelation at lag 1, which is named `AC_1`, for example.
Details of how to name, label, and structure these dependencies are provided in this section.

A given highly comparative time-series analysis requires the user to specify a set of code to evaluate (*master operations*), and their associated individual outputs (*operations*), and a time-series database (*time series*).
We provide a default library of over 9,000 *operations* (derived from approximately 1,300 unique *master operations*) with the *hctsa* package.
This can be customized, and additional pieces of code can also be added to the repository, in addition to adding the time series making up the dataset to be analyzed.

Assigning keywords to time series makes it easier to retrieve a set of time series with a given set of keywords for analysis, and to group time series annotated with different keywords for classification tasks.

As described above, in our system, a *master operation* refers to a piece of Matlab code and a set of input parameters.
Valid outputs from a master operation are:
1. A real number,
2. A structure containing real numbers,
3. **NaN** to indicate that the input time series is not appropriate for this code.

The (potentially many) outputs from a master operation can thus be mapped to individual operations (or features), which are single real numbers summarizing a time series that make up individual columns of the resulting data matrix.

## Working with HCTSA files
Each `HCTSA_*.mat` file includes the following key elements:

-   **TS_DataMat** is an *n* x *m* matrix corresponding to the *n* time series and *m* operations retrieved from the database. Elements of **TS_DataMat** correspond to the result of applying each operation to each time series.

-   **TS_Quality** is an *n* x *m* matrix containing quality labels for each operation output. Quality labels are described in the section below.

-   **TS_CalcTime** is an *n* x *m* matrix containing calculation times for each operation output. Note that for operations that point to a structure produced by a master operation operations, the calculation time stored is that taken to compute the entire master function from which they were derived.

-   **TimeSeries** is a structure array containing information about the time series retrieved (corresponding to rows of the **TS_** matrices).

-   **Operations** is a structure array containing information about the operations retrieved (columns of the **TS_** matrices).

-   **MasterOperations** is a structure array containing information about the master operations retrieved, corresponding to the code evaluated that is referenced by each operation.


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
