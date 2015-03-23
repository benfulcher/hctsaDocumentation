# Populating the database with time series and operations
<!--{#sec:PopulatingDatabase}-->

Adding time series, code files (called *master operations*), and operations (or *features*) to the database is done using the function `SQL_add`.
It has two key inputs that specify:

1. Whether to import a set of time series (specify `‘ts’`), a set of operations (specify `‘ops’`), or a set of master operations (specify `‘mops’`),
2. The name of the input text file that contains appropriately-formatted information about the time series, master operations, or operations.

Note that manually adding or deleting rows to the database can create inconsistencies and errors in the database structure and should not be done unless you *really* know what you’re doing.
Adding time series and operations to the database should always be done using `SQL_add` which sets up the **Results** table of the database and ensures that the indexing relationships in the database
are properly maintained.
We deal with adding [master operations](#sec:addingMops), [operations](#sec:addingOps), and [time series](#sec:addingTimeSeries) separately.
Users wishing to run the existing library of code on their own set of time series will only need to add time series, as the full operation library is added in the `install.m` script.