# Compiling binaries

Some external code packages require compiled binary code to be used. The _mex_ code, the _TISEAN_ package binaries, and the _ripser_ binary are all compiled automatically, in one step, by the `install` script.
There is no separate manual compilation process for any of them on macOS or Linux.

## What you need

* C and C++ compilers set up for Matlab's _mex_ (configure them using `mex -setup` and `mex -setup C++`; cf. `doc mex`), for the _mex_ code.
* `gcc`, `make`, and a Fortran compiler (`gfortran`) for _TISEAN_ (four of the _TISEAN_ binaries used by _hctsa_ are written in Fortran).
* A C++ compiler (e.g., `g++` or `clang++`) for _ripser_.

On macOS, install the Xcode Command Line Tools (`xcode-select --install`) and `gfortran` (e.g., `brew install gcc`).
On Debian/Ubuntu, install `build-essential` and `gfortran` (`sudo apt-get install build-essential gfortran`).

At the end of compilation, `install` (via `compile_mex`) prints a summary of which components compiled successfully (`[ OK ]`) or failed (`[FAIL]`).
_hctsa_ still runs if some components fail to compile, but the operations that depend on them will not work.

## Compiling mex code

Many of the operations (especially external code packages) rely on _mex_ functions (pieces of code written in C or C++), that need to be compiled to run natively on a given system architecture. To ensure that as many operations as possible run successfully on your data, you should compile these _mex_ functions for your system.

Once mex is set up, running `install` (from the repository root) compiles all the mex functions used across the code repository, via `compile_mex` (in the `Toolboxes` directory).
If the optional _catch22_ submodule is present, `compile_mex` also compiles its _mex_ files.

## Compiling the _TISEAN_ binaries

Some operations rely on the [_TISEAN_ nonlinear time-series analysis package](http://www.mpipks-dresden.mpg.de/~tisean/Tisean_3.0.1/index.html).
As part of the same `install` step (`compile_mex` calling `compile_tisean`), _hctsa_ compiles _TISEAN_ from source directly into `Toolboxes/Tisean_3.0.1/bin`, with no command-line steps needed.
Only 12 _TISEAN_ binaries are built (`c1`, `c2d`, `c2g`, `c2t`, `d2`, `nstat_z`, `false_nearest`, `boxcount`, `lyap_r`, `lyap_spec`, `poincare`, `nrlazy`), not the full _TISEAN_ suite.

If `compile_tisean` is the only part of `install` that fails (e.g., because a required compiler is missing), you can fix the underlying issue and re-run `compile_tisean` on its own from inside the `Toolboxes` directory, rather than re-running the whole of `install`.

`startup` adds the compiled _TISEAN_ binaries (`Toolboxes/Tisean_3.0.1/bin`) to the system path for the current Matlab session.
If installation was successful, you should be able to access them from Matlab, e.g., typing `!which lyap_r` should return a path inside `Toolboxes/Tisean_3.0.1/bin`.

## Compiling _ripser_

The `NL_PersistentHomology` operations use Ulrich Bauer's [_ripser_](https://github.com/Ripser/ripser) persistent-homology program.
`install` (via `compile_mex` calling `compile_ripser`) compiles it from `Toolboxes/ripser/ripser.cpp` into `Toolboxes/ripser/bin`, which `startup` also adds to the system path.
If only this step fails, fix the issue and re-run `compile_ripser` from inside the `Toolboxes` directory.

## Windows

Automatic compilation of _TISEAN_ and _ripser_ is not supported on Windows: `install` reports an error for these two components.
In _hctsa_ v3.0.0, the operations that use them can't be run on Windows, so skip them, as described below.
The other operations don't need these binaries.

## Ignoring _TISEAN_ and _ripser_ functions

If you'd prefer not to use functions from the _TISEAN_ package (e.g., to avoid the compilation step, or because you don't need nonlinear time-series methods), you should initialize your dataset with the TISEAN functions removed.
You could do this by removing them from a copy of the default operations input file, `INP_ops_hctsa.txt` (in the `FeatureSets` directory), and initializing your dataset with your custom input files, e.g., `TS_Init('INP_ts.mat',{'INP_mops_hctsa.txt','INP_ops_custom.txt'})`.
Alternatively, you could remove them from your initialized _hctsa_ dataset by filtering on the `'tisean'` keyword.

For example, to filter a local Matlab _hctsa_ file (e.g., `HCTSA.mat`), you can use the following: `TS_LocalClearRemove('raw','ops',TS_GetIDs('tisean','raw','ops'),true);`, which will remove all operations with the 'tisean' keyword from the _hctsa_ dataset in `HCTSA.mat`.
The `PP_SchreiberDenoise` operations also use _TISEAN_ (`nrlazy`) but do not carry the `'tisean'` keyword, so remove them by name:

```
load('HCTSA.mat','Operations');
TS_LocalClearRemove('raw','ops',Operations.ID(startsWith(Operations.Name,'PP_SchreiberDenoise')),true);
```

Similarly, the operations that use _ripser_ (`NL_PersistentHomology`) carry the `'topology'` keyword, and can be removed using `TS_LocalClearRemove('raw','ops',TS_GetIDs('topology','raw','ops'),true);`.
