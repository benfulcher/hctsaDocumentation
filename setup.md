# Getting started

The _hctsa_ package can be used *completely within Matlab*, allowing users to analyse time-series datasets quickly and easily.
Here we will focus on this Matlab-based use of the software, but note that, for larger datasets requiring distributed computing set-ups, or datasets that may grow with time, _hctsa_ can also be linked to a *mySQL* database, as described in [a dedicated chapter](overview_mysql_database.md).

<!-- Here we will focus on using the software completely in Matlab, which is much simpler than setting up and using a *mySQL* database, and avoids the overheads of reading and writing large quantities of data between Matlab and a database.
Instructions for performing distributed computations using _hctsa_ and a *mySQL* database are in [a dedicated chapter](overview_mysql_database.md). -->

## Installing the _hctsa_ package
The simplest way to get the _hctsa_ package up and running is to run the `install` script, which adds the required paths to dependent time-series packages (toolboxes), and compiles *mex* binaries to work on your system architecture.
Once this one-off installation step is complete, you're ready to go! (NB: to include additional functions from the TISEAN nonlinear time-series analysis package, you'll also need to [compile TISEAN routines](compiling_binaries.md)).

After installation, future use of the package can begin by opening Matlab, navigating to the _hctsa_ package, and then loading the paths required by the _hctsa_ package by running the `startup` script.

<!------->


<!--1. Sets up a *mySQL* server and database, populates the database with our standard library of functions and operations, and then compiles all of the mex functions required by Matlab to run all of the operations.-->

<!--## Setting up-->
<!--{#sec:SettingUp}-->

<!--This section describes initial tasks that one must perform once, to set up the *mySQL* database and its interface with Matlab.-->


<!--### Setting the path-->
<!-- {#sec:settingPath} -->
