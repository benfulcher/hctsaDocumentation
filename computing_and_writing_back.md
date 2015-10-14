# Computing operations on time series and writing back to the database

After retrieving data from the *mySQL* database, missing entries (NULL in the database, and NaN in the local Matlab file) can be computed using `TS_compute`, and stored back to the database using `SQL_store`.
These functions are described below.

## Performing calculations using `TS_compute`
<!--{#sec:performing_calculations}-->

Values retrieved using `SQL_retrieve` (to the local `HCTSA_loc.mat` file) that have not previously been calculated are evaluated using `TS_compute`, as described [here](computing_results.md).
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