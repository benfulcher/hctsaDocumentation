# Getting started

At the start of any analysis using the *hctsa* package, required directories must be added to the *Matlab* path using the `startup.m` script.
This function keeps track of functions for operations, for analysis, third-party toolboxes, and time-series data files, which could be easily altered at any point.

## Quick start: Installing the *hctsa* code package

The *hctsa* package requires some preliminary set up to work with a *mySQL* database, described [here](mysql_database.md):

1. Installation of *mySQL*, either locally, or on an accessible server.
2. Setting up Matlab with a *mySQL* java connector (done by running the `install_jconnector` script in the **Database** directory and then restarting Matlab).

After the database is set up, the rest of the package can be installed by running the `install.m` script, which:

1. [if required] Sets up Matlab to be able to communicate with the *mySQL* server and creates a new database to store Matlab calculations in, described [here](mysql_database.md).
2. Populates the database with our default library of master operations and operations (using `SQL_add` commands), described [here](populating.md).
3. Compiles **mex** binaries required to evaluate all operations, described [here](compiling_binaries.md). In addition to the mex files compiled by the `install` script, the user is additionally required to compile the *TISEAN* binaries if desired [in the commandline](compiling_binaries.md).

This section contains additional details about each of these steps.

Once installed, if using our default library of operations, the typical next step is to add a dataset of time series using the `SQL_add` command, described [here](adding_time_series.md).
Custom [master operations](adding_master_operations.md) and [operations](adding_operations.md) can also be added, if required.


## Next steps

After installing and importing a time-series dataset, 


<!--1. Sets up a *mySQL* server and database, populates the database with our standard library of functions and operations, and then compiles all of the mex functions required by Matlab to run all of the operations.-->

<!--## Setting up-->
<!--{#sec:SettingUp}-->

<!--This section describes initial tasks that one must perform once, to set up the *mySQL* database and its interface with Matlab.-->


<!--### Setting the path-->
<!-- {#sec:settingPath} -->