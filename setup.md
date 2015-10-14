# Getting started

The *hctsa* package can either be used in two ways:

1. *Completely within Matlab*, recommended for quick and easy analysis of a particular time-series dataset
2. *Using a link between Matlab and a mySQL database*, recommended for larger datasets requiring distributed computing set-ups, or datasets that may grow with time.

Here we will mainly focus on using the software completely in Matlab; instructions for interacting with a mySQL database are in [a separate chapter](overview_mysql_database.md).

## Installing the *hctsa* package
This is the simplest way to get the *hctsa* up and running.
The *hctsa* package can be installed by running the `install` script, which adds the required paths to dependent time-series packages (toolboxes), and compiles mex binaries to work on your system.
Once this one-off installation step is complete, you're ready to go! (NB: to also include additional functions from the TISEAN pacakge, you'll need to [compile TISEAN routines](compiling_tisean.md)).

Future use of the package can begin by simply loading the paths required by the *hctsa* package using the `startup` script.

## Overview of an analysis

### Initializing a time-series dataset using `TS_init`
When running *hctsa* within Matlab, the first step of any analysis is to initialize a **HCTSA_loc.mat** file, which contains all of the information about the set of time series and operations in your analysis, and stores the results of applying all operations to all time series.
This can be achieved using the `TS_init` function, whose arguments specify the [input files](inputFiles.md) for this information.
An example usage is as follows:
```
TS_init('INP_test_ts.mat');
```
where details of a time-series dataset are specified in the **INP_test_ts.mat** file.
By default, `TS_init` will load our full library of operations as features (which are detailed in the files **INP_mops.txt** and **INP_ops.txt**); custom input files can be used to specify custom sets of operations.

### Computation, processing, and analysis

After initializing an **HCTSA_loc.mat** file, [computing all operations on all time series](running_computations.md) is done using `TS_compute`, which stores the results back to this local file.

The results are structured in local Matlab files containing matrices (that store the results of the computations) and structure arrays (that store information about the time-series data and operations), as described [here](hctsa_structure.md).

After the computation is complete, [a range of processing, analysis, and plotting functions](analyzing_visualizing.md) are also provided with the software.

---


<!--1. Sets up a *mySQL* server and database, populates the database with our standard library of functions and operations, and then compiles all of the mex functions required by Matlab to run all of the operations.-->

<!--## Setting up-->
<!--{#sec:SettingUp}-->

<!--This section describes initial tasks that one must perform once, to set up the *mySQL* database and its interface with Matlab.-->


<!--### Setting the path-->
<!-- {#sec:settingPath} -->
