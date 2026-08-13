# Compiling binaries

Some external code packages require compiled binary code to be used. Both the _mex_ code and the _TISEAN_ package binaries are now compiled automatically, in one step, by the `install` script — there is no separate manual compilation process for either.

## Compiling mex code

Many of the operations (especially external code packages) rely on _mex_ functions (pieces of code written in C or fortran), that need to be compiled to run natively on a given system architecture. To ensure that as many operations as possible run successfully on your data, you should compile these _mex_ functions for your system. This requires working compilers (e.g., gcc, g++) to be installed on your system, which can be configured using `mex -setup` (cf. `doc mex` for more information).

Once mex is set up, running `install` (from the repository root) compiles all the mex functions used across the code repository, via `compile_mex`.

## Compiling the _TISEAN_ binaries

Some operations rely on the [_TISEAN_ nonlinear time-series analysis package](http://www.mpipks-dresden.mpg.de/~tisean/Tisean_3.0.1/index.html). As part of the same `install` step (`compile_mex` calling `compile_tisean`), _hctsa_ now compiles TISEAN from source directly into `Toolboxes/Tisean_3.0.1/bin` and adds that directory to the system path — no command-line steps, no platform-specific instructions, and no separate Windows/Cygwin workaround are needed. Only the specific TISEAN binaries that _hctsa_ operations actually call are built (`c1`, `c2d`, `c2g`, `c2t`, `d2`, `nstat_z`, `false_nearest`, `boxcount`, `lyap_r`, `lyap_spec`, `poincare`), not the full TISEAN suite.

If `compile_tisean` is the only part of `install` that fails (e.g. because a required compiler is missing), you can fix the underlying issue and re-run `compile_tisean` on its own from the `Toolboxes` directory, rather than re-running the whole of `install`.

If installation was successful, `startup.m` will report the path to the compiled TISEAN binaries, and you should be able to access them from Matlab, e.g., typing `!which poincare` should return a path inside `Toolboxes/Tisean_3.0.1/bin`.

### Ignoring _TISEAN_ functions

If you'd prefer not to use functions from the _TISEAN_ package (e.g., to avoid the compilation step, or because you don't need nonlinear time-series methods), you should initialize your dataset with the TISEAN functions removed. You could do this by removing them from your `INP_ops.txt` file when initializing your dataset, or you could remove them from your initialized _hctsa_ dataset by filtering on the `'tisean'` keyword.

For example, to filter a local Matlab _hctsa_ file (e.g., `HCTSA.mat`), you can use the following: `TS_LocalClearRemove('raw','ops',TS_GetIDs('tisean','raw','ops'),true);`, which will remove all operations with the 'tisean' keyword from the _hctsa_ dataset in `HCTSA.mat`.
