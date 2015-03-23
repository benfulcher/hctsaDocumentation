# Getting started

At the start of any analysis using the *hctsa* package, required directories must be added to the *Matlab* path using the `startup.m` script.
This function keeps track of functions for operations, for analysis, third-party toolboxes, and time-series data files, which could be easily altered at any point.

## Installing the *hctsa* code package for the first time

The *hctsa* package should be set up by running the `install` script, which:

1. Installs and sets up a *mySQL* server, setup a new database to store Matlab calculations in, and set up Matlab to be able to communicate with it, [here](mysql_database.md).
1. Sets up a *mySQL* server and database, populates the database with our standard library of functions and operations, and then compiles all of the mex functions required by Matlab to run all of the operations.

<!--## Setting up-->
<!--{#sec:SettingUp}-->

<!--This section describes initial tasks that one must perform once, to set up the *mySQL* database and its interface with Matlab.-->


This section contains additional details about each of the steps performed by `install.m`:

2. Populate the database with our default library of master operations and operations (using `SQL_add` commands), [here](populating.md).
3. Compiling **mex** binaries required to evaluate all operations [here](compiling_binaries.md). The user will need to compile the *TISEAN* binaries separately to the `install.m` script, as explained [here](compiling_binaries.md).


<!--### Setting the path-->
<!-- {#sec:settingPath} -->