# Populating the database with time series and operations using `SQL_add`
<!--{#sec:PopulatingDatabase}-->

Adding time series, code files (called *master operations*), and operations (or *features*) to the database are achieved using the function `SQL_add`.
It has two key inputs that specify:

1. Whether to import a set of time series (specify `‘ts’`), a set of operations (specify `‘ops’`), or a set of master operations (specify `‘mops’`),
2. The name of the input text file that contains appropriately-formatted information about the time series, master operations, or operations.

In this section, we describe how to add [master operations](adding_master_operations.md), [operations](adding_operations.md), and [time series](adding_time_series.md) separately.

Users wishing to run the default *hctsa* library of code on their own set of time series will only need to add time series, as the full operation library is added in the `install.m` script.

Note that manually editing the database, including adding or deleting rows, is very dangerous, as it can create inconsistencies and errors in the database structure.
Adding time series and operations to the database should always be done using `SQL_add` which sets up the **Results** table of the database and ensures that the indexing relationships in the database
are properly maintained.
