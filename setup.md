# Getting started

The *hctsa* package can either be used in two ways:

1. *Completely within Matlab*, recommended for quick and easy analysis of a particular time-series dataset
2. *Using a link between Matlab and a mySQL database*, recommended for larger datasets requiring distributed computing set-ups, or datasets that may grow with time.

Here we will mainly focus on using the software completely in Matlab, which is much simpler than setting up and using a *mySQL* database, and avoids the overheads of reading and writing large quantities of data between Matlab and a database.
Instructions for interacting with a *mySQL* database are in [a separate chapter](overview_mysql_database.md).

## Installing the *hctsa* package
The simplest way to get the *hctsa* package up and running is to run the `install` script, which adds the required paths to dependent time-series packages (toolboxes), and compiles *mex* binaries to work on your system architecture.
Once this one-off installation step is complete, you're ready to go! (NB: to include additional functions from the TISEAN nonlinear time-series analysis package, you'll also need to [compile TISEAN routines](compiling_binaries.md)).

After installing, future use of the package can begin by simply loading the paths required by the *hctsa* package using the `startup` script.

<!------->


<!--1. Sets up a *mySQL* server and database, populates the database with our standard library of functions and operations, and then compiles all of the mex functions required by Matlab to run all of the operations.-->

<!--## Setting up-->
<!--{#sec:SettingUp}-->

<!--This section describes initial tasks that one must perform once, to set up the *mySQL* database and its interface with Matlab.-->


<!--### Setting the path-->
<!-- {#sec:settingPath} -->
