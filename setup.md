# Getting started

At the start of any analysis using the *hctsa* package, required directories must be added to the *Matlab* path using the `startup.m` script.
This function keeps track of functions for operations, for analysis, third-party toolboxes, and time-series data files, which could be easily altered at any point.

## Quick start: Installing the *hctsa* code package for the first time

The *hctsa* package requires some preliminary set up to work with a *mySQL* database:

1. Installation of *mySQL*, either locally, or on an accessible server (must be done independently).
2. Setting up Matlab with a *mySQL* java connector (done by running the `install_jconnector` script).

should be set up by running the `install.m` script, which:

1. Installs and sets up a *mySQL* server, creates a new database to store Matlab calculations in, and sets up Matlab to be able to communicate with it, described [here](mysql_database.md).
2. Populate the database with our default library of master operations and operations (using `SQL_add` commands), described [here](populating.md).
3. Compiling **mex** binaries required to evaluate all operations, described [here](compiling_binaries.md). In addition to the mex files compiled in the `install.m` script, the user is additionally required to compile the *TISEAN* binaries [in the commandline](compiling_binaries.md).

This section contains additional details about each of these steps.

<!--1. Sets up a *mySQL* server and database, populates the database with our standard library of functions and operations, and then compiles all of the mex functions required by Matlab to run all of the operations.-->

<!--## Setting up-->
<!--{#sec:SettingUp}-->

<!--This section describes initial tasks that one must perform once, to set up the *mySQL* database and its interface with Matlab.-->


<!--### Setting the path-->
<!-- {#sec:settingPath} -->