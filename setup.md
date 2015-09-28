# Getting started

The *hctsa* package can either be used in two ways:

1. *Completely within Matlab*, recommended for quick and easy analysis of a particular time-series dataset
2. *Using a link between Matlab and a mySQL database*, recommended for larger datasets requiring distributed computing set-ups, or datasets that may grow with time.

We will mostly focus on using the software completely in Matlab; instructions for interacting with a mySQL database are in a separate chapter, [here](overview_mysql_database.md).

## Installing the *hctsa* package
This is the simplest way to get the *hctsa* up and running.
The *hctsa* package can be installed by running the `install` script, which adds the required paths to dependent toolboxes, and compiles mex binaries to work on your system.
Once this one-off installation step is complete, you're ready to go! (NB: you can optionally [compile TISEAN routines](compiling_tisean.md) for your system to have access to additional operations).

Future use of the package can begin by simply loading the paths required by the *hctsa* package using the `startup` script.

### Initializing a time-series dataset using `TS_init`
When running *hctsa* within Matlab, the first step of any analysis is to initialize a **HCTSA_loc.mat** file, which contains all of the information about the time series and operations, and the results of applying all operations to all time series.
This can be achieved using the `TS_init` function, whose arguments specify the [input files](inputFiles.md) for this information.
An example usage is as follows:
```
TS_init('INP_test_ts.mat');
```
where details of a time-series dataset are specified in the **INP_test_ts.mat** file.
By default, `TS_init` will load our full library of operations as features (which are detailed in the files **INP_mops.txt** and **INP_ops.txt**); custom input files can be used to specify custom sets of operations.

### Computation, processing, and analysis

After initializing a **HCTSA_loc.mat** file, computing all operations on all time series is done [within Matlab](computing_and_writing_back.md) using `TS_compute`, which stores the results back to this local file.

While this is running, you may like to get a feel for how the results will be structured, as local Matlab files containing matrices (for storing the results) and structure arrays (for storing information about the time-series data and operations), [here](hctsa_structure.md).

After the computation is complete, a range of processing, analysis, and plotting functions are also provided with the software, as described [here](analyzing_visualizing.md).

---


<!--1. Sets up a *mySQL* server and database, populates the database with our standard library of functions and operations, and then compiles all of the mex functions required by Matlab to run all of the operations.-->

<!--## Setting up-->
<!--{#sec:SettingUp}-->

<!--This section describes initial tasks that one must perform once, to set up the *mySQL* database and its interface with Matlab.-->


<!--### Setting the path-->
<!-- {#sec:settingPath} -->
