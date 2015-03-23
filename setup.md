## Installing the *hctsa* code package

The *hctsa* package should be set up by running the `install` script, which sets up a *mySQL* server and database, populates the database with our standard library of functions and operations, and then compiles all of the mex functions required by Matlab to run all of the operations.

<!--## Setting up-->
<!--{#sec:SettingUp}-->

This section describes initial tasks that one must perform once, to set up the *mySQL* database and its interface with Matlab.

### Setting the path
<!-- {#sec:settingPath} -->

A useful way to keeping track with the directories you need to add to Matlab’s path involves creating a `.m` file containing `addpath` commands that can be run at the start of each Matlab session.
We used this to keep track of the code for operations, for analysis, third-party toolboxes, and time-series data files, which could be easily altered at any point.
An example is given in the code accompanying this document, as `startup.m`.
