# Computing operations on time series and writing back to the database

After retrieving data from the *mySQL* database, missing entries (NULL in the database, and NaN in the local Matlab file) can be computed using `TS_compute`, and stored back to the database using `SQL_store`.
These functions are described below.

## Performing calculations using `TS_compute`
<!--{#sec:performing_calculations}-->

Once a relevant section of the data matrix has been retrieved, we can calculate values that have not previously been calculated, indicated by NULL entries in the **Results** table of the database, and NaN entries in the corresponding `HCTSA_loc.mat`.

Calculations are performed using the function `TS_compute`, which stores results back into the matrices in `HCTSA_loc`.
This function can be run without inputs:

    % Compute missing values in HCTSA_loc.mat:
    TS_compute;

Or, by specifying the first input to 1 to calculate across available cores using the Matlab Parallel Processing Toolbox:

    % Compute missing values in HCTSA_loc.mat using parallel processing:
    TS_compute(1);

Running `TS_compute` will begin running operations on time series in **HCTSA_loc.mat** for which elements in **TS\_DataMat** are **NaN**s (indicating that they have not been run before) or have a [quality label](retrieving_to_compute.md) of 1 (indicating a prior error).
The results will be stored back in the matrices of **HCTSA_loc.mat**, i.e., **TS\_DataMat** (output of each operation on each time series), **TS\_CalcTime** (calculation time for each operation on each time series), and **TS\_Quality** (labels indicating errors or special-valued outputs).

When all NULL entries in **TS\_DataMat** have been calculated, **TS_compute** saves the results back to the local file: **HCTSA_loc.mat**.
These results can then be inspected directly (if needed), or simply written back to the database using `SQL_store`, as described below.

## Writing calculations back to the database using `SQL_store`
<!--{#sec:writingCalcsDatabase}-->

Once calculations have been performed using Matlab on local files, the results must be written back to the database.
This task is performed by `SQL_store`, which reads the data in `HCTSA_loc.mat`, checks that the metadata still matches the database, and then begins updating the **Output**, **Quality**, and **CalculationTime** columns of the **Results** table in the *mySQL* database.
This can be done by simply running:

        SQL_store;

Depending on database latencies, this can be a relatively slow process, up to 20-25 s per time series, updating each row in the **Results** table individually using *mySQL* **UPDATE** statements.
However, the delay in this step means that the computation can be distributed across multiple compute nodes, and that stored data can be indexed and retrieved systematically.
Keeping results in local Matlab files can be extremely inefficient, and can indeed be untenable for large datasets.