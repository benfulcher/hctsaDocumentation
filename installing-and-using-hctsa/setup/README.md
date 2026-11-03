# Installing and setting up

The _hctsa_ package is used _completely within Matlab_, allowing users to analyze time-series datasets quickly and easily, working with local `.mat` files.

## Requirements

* **Matlab R2022a or later.**
* **Matlab toolboxes.** The Statistics and Machine Learning Toolbox is required.
  Some features also use the Signal Processing, Curve Fitting, Wavelet, and Econometrics Toolboxes; without them, _hctsa_ still runs, but the features that need them will not be computed.
  The Parallel Computing Toolbox is optional, and is used to run computations in parallel (e.g., `TS_Compute('series')`).
* **Compilers.** Compiling the _mex_ files and the external binaries used by some features (TISEAN and ripser) needs C and C++ compilers (set up for Matlab with `mex -setup`), a Fortran compiler (e.g., `gfortran`), and `make`, as described in [compiling binaries](compiling_binaries.md).

## Getting the code

We recommend cloning the repository with git, including its submodules:

```
git clone --recurse-submodules https://github.com/benfulcher/hctsa.git
```

The [_catch22_](https://github.com/DynamicsAndNeuralSystems/catch22) feature set is included as an optional git submodule.
No feature in the default _hctsa_ library needs it, but it is required for the reduced `'catch22'` (and `'catch24'`) feature sets, e.g., `TS_Init('INP_ts.mat','catch22')`.
If you cloned without `--recurse-submodules`, `startup` fetches _catch22_ the first time it runs in an interactive Matlab session (set the environment variable `HCTSA_NO_SUBMODULE_FETCH` to skip this), or you can fetch it yourself by running `git submodule update --init --recursive` from the _hctsa_ directory.

To download rather than clone, use the `hctsa-v3.0.0.tar.gz` archive attached to the [v3.0.0 release](https://github.com/benfulcher/hctsa/releases/tag/v3.0.0), which includes _catch22_.
GitHub's automatic "Source code" archives do not include the _catch22_ submodule.

## Installing the _hctsa_ package

The simplest way to get the _hctsa_ package up and running is to run the `install` script from the _hctsa_ directory.
It adds the required paths (by running `startup`), checks which of the relevant Matlab toolboxes are installed, and compiles the _mex_ files and the external TISEAN and ripser binaries to work on your system architecture (cf. [compiling binaries](compiling_binaries.md)).
At the end, it offers to run a quick (about one minute) test that computes the full feature set for a synthetic time series.
Running `install(true)` does the same without pausing for key presses (and skips the test), e.g., for scripted installations.
Once this one-off installation step is complete, you're ready to go!
If you update _hctsa_ to a new version, re-run `install`, because some of the compiled code may have changed.

After installation, future use of the package can begin by opening Matlab, navigating to the _hctsa_ package, and then loading the paths required by the _hctsa_ package by running the `startup` script.
Run `startup` in each new Matlab session: as well as adding the Matlab paths, it adds the compiled TISEAN and ripser binaries to the system path for that session.
