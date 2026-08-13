# Installing and setting up

The _hctsa_ package is used _completely within Matlab_, allowing users to analyse time-series datasets quickly and easily, working with local `.mat` files.

## Installing the _hctsa_ package

The simplest way to get the _hctsa_ package up and running is to run the `install` script, which adds the required paths to dependent time-series packages \(toolboxes\), and compiles _mex_ binaries (including the TISEAN nonlinear time-series analysis package, cf. [compiling binaries](compiling_binaries.md)) to work on your system architecture. Once this one-off installation step is complete, you're ready to go!

After installation, future use of the package can begin by opening Matlab, navigating to the _hctsa_ package, and then loading the paths required by the _hctsa_ package by running the `startup` script.

