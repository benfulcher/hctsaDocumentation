# Installing and setting up

The _hctsa_ package can be used _completely within Matlab_, allowing users to analyse time-series datasets quickly and easily. Here we will focus on this Matlab-based use of the software, but note that, for larger datasets requiring distributed computing set-ups, or datasets that may grow with time, _hctsa_ can also be linked to a _mySQL_ database, as described in [a dedicated chapter](../overview_mysql_database/).

## Installing the _hctsa_ package

The simplest way to get the _hctsa_ package up and running is to run the `install` script, which adds the required paths to dependent time-series packages \(toolboxes\), and compiles _mex_ binaries to work on your system architecture. Once this one-off installation step is complete, you're ready to go! \(NB: to include additional functions from the TISEAN nonlinear time-series analysis package, you'll also need to [compile TISEAN routines](compiling_binaries.md)\).

After installation, future use of the package can begin by opening Matlab, navigating to the _hctsa_ package, and then loading the paths required by the _hctsa_ package by running the `startup` script.

