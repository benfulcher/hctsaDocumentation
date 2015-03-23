# Populating the database with time series and operations using `SQL_add`
<!--{#sec:PopulatingDatabase}-->

## Overview

As explained [above](database_structure.md), the *mySQL* database is structured into 3 parts:

1. *Master Operations* specify pieces of code (Matlab functions) and their inputs to be computed. Taking in a time series, master operations often generate a large number of outputs, each of which can be identified with an *operation* (or *feature*).
2. *Operations* (or *features*) are a single number summarizing some measure of structure in a time series. In *hctsa*, each operation links to an output from a piece of evaluated code (a *master operation*).
3. *Time series* are univariate, uniformly sampled, time-ordered measurements. The data, and keyword labels for each time series are stored in the database.

These three different objects are summarized below:

| **Master Operation** | **Operation** | **Time Series** |
|:-------------:|:-------------:|:-------------:|
| 0 | No problems with calculation. Output was a real number. |
| 1 | When running the code, a fatal error was encountered. |
| 2 | Output of the code was `NaN`.|
| 3 | Output of the code was `Inf`. |
| 4 | Output of the code was `-Inf` |
| 5 | Output had a nonzero imaginary component |

A given highly comparative time-series analysis requires the user to specify a set of code to evaluate (*master operations*), and their associated individual outputs (*operations*), and a time-series database (*time series*).
We provide a default library of approximately 9000 *operations* (derived from approximately 1300 unique *master operations*) with the *hctsa* package.
This can be customized, and additional pieces of code can also be added to the repository, in addition to adding the time series making up the dataset to be analyzed.
This is achieved using `SQL_add` commands, as described below.

## Using `SQL_add`

Adding time series, master operations, and operations to the database are achieved using the function `SQL_add`.
It has two key inputs that specify:

1. Whether to import a set of time series (specify `‘ts’`), a set of operations (specify `‘ops’`), or a set of master operations (specify `‘mops’`),
2. The name of the input text file that contains appropriately-formatted information about the time series, master operations, or operations to be imported.

In this section, we describe how to use `SQL_add` to add [master operations](adding_master_operations.md), [operations](adding_operations.md), and [time series](adding_time_series.md) to the database.

Users wishing to run the default *hctsa* code library their own time-series dataset will only need to [add time series](adding_time_series.md) to the database, as the full operation library is added by the `install.m` script.
Users wishing to add additional features using custom time-series code or different types of inputs to existing code files, can either edit the default *INP_ops.txt* and *INP_mops.txt* files provided with the repository, or create new input files for their custom analysis methods (as explained for [operations](adding_operations.md) and [master operations](adding_master_operations.md)).

***REMINDER***: Manually editing the database, including adding or deleting rows, is very dangerous, as it can create inconsistencies and errors in the database structure.
Adding time series and operations to the database should only be done using `SQL_add` which sets up the **Results** table of the database and ensures that the indexing relationships in the database
are properly maintained.
