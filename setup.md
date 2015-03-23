## Installing the *hctsa* code package

The *hctsa* package should be set up by running the `install` script, which sets up a *mySQL* server and database, populates the database with our standard library of functions and operations, and then compiles all of the mex functions required by Matlab to run all of the operations.

<!--## Setting up-->
<!--{#sec:SettingUp}-->

<!--This section describes initial tasks that one must perform once, to set up the *mySQL* database and its interface with Matlab.-->

This seciton contains additional details about each of the steps performed by `install.m`:

1. Install and set up a *mySQL* server, setup a new database to store Matlab calculations in, and set up Matlab to be able to communicate with it, [here](mysql_database.md).
2. Populate the database with master operations, operations, and time series (using the `install` script followed by the user importing their own time series using `SQL_add`), [here](populating.md).
3. Run computations to evaluate the operations on all the time series using the `sample_runscript` as a template, [here](sec:calculating).
4. Analyze the results of computations by retrieving calculated data using `TSQ_prepared`, normalizing and filtering it using `TSQ_normalize`, and then making use of a range of analysis and visualization scripts provided, [here](sec:analyzing).

### Setting the path
<!-- {#sec:settingPath} -->

A useful way to keeping track with the directories you need to add to Matlab’s path involves creating a `.m` file containing `addpath` commands that can be run at the start of each Matlab session.
We used this to keep track of the code for operations, for analysis, third-party toolboxes, and time-series data files, which could be easily altered at any point.
An example is given in the code accompanying this document, as `startup.m`.
