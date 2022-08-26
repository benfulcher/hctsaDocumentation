# Working with a *mySQL* database

When running large-scale _hctsa_ computations, it can be useful to set up a *mySQL* database for time series, operations, and the computation results, and have many Matlab instances (running on different nodes of a compute cluster, for example) communicate directly with the database.

The _hctsa_ software comes with this (optional) functionality, allowing a more powerful, distributed way to compute and store results of a large-scale computation.

This chapter outlines the steps involved in setting up, and running _hctsa_ computations using a linked *mySQL* database.

## Installing the _hctsa_ code package to work with a *mySQL* database

The _hctsa_ package requires some preliminary set up to work with a *mySQL* database, described [here](setup_mysql_database.md):

1. Installation of *mySQL*, either locally, or on an accessible server.
2. Setting up Matlab with a *mySQL* java connector (done by running the `install_jconnector` script in the **Database** directory, and then restarting Matlab).

After the database is set up, and the packages required by _hctsa_ are installed (by running the `install` script), linking to a *mySQL* database can be done by running the `install_database` script, which:

1. Sets up Matlab to be able to communicate with the *mySQL* server and creates a new database to store Matlab calculations in, described [here](setup_mysql_database.md).
2. Populates the database with our default library of master operations and operations, as described [here](populating.md). (NB: a description of the terminology of 'master operations': a set of input arguments to an analysis function, and 'operations': a single time-series feature, is [here](populating.md)).
<!-- 3. Compiles **mex** binaries required to evaluate all operations, described [here](compiling_binaries.md). In addition to the mex files compiled by the `install` script, the user is additionally required to compile the *TISEAN* binaries if desired [in the commandline](compiling_binaries.md). -->

This section contains additional details about each of these steps.

Note that the above steps are one-off installation steps; once the software is installed and compiled, a typical workflow will simply involve opening Matlab, running the `startup` script (which adds all paths required for the _hctsa_ software), and then working within Matlab from any desired directory.

### Adding a time-series dataset

Once installed using our default library of operations, the typical next step is to [add a dataset of time series](adding_time_series.md) to the database using the `SQL_Add` command.
Custom [master operations](adding_master_operations.md) and [operations](adding_operations.md) can also be added, if required.

### Computation, processing, and analysis

After installing the software and importing a time-series dataset to a *mySQL* database, the process by which data is retrieved from the database to local Matlab files (using `SQL_Retrieve`), feature sets computed within Matlab (using `TS_Compute`), and computed data stored back in the database (`SQL_store`) is described in detail [here](calculating.md).

After the computation is complete for a time-series dataset, a range of processing, analysis, and plotting functions are also provided with the software, as described [here](analyzing_visualizing.md).
