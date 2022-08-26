# Compiling binaries

Some external code packages require compiled binary code to be used.
Compilation of the mex code is handled by `compile_mex` as part of the `install` script, but the *TISEAN* package binaries need to be compiled separately in the command line.

## Compiling mex code

Many of the operations (especially external code packages) rely on *mex* functions (pieces of code written in C or fortran), that need to be compiled to run natively on a given system architecture.
To ensure that as many operations as possible run successfully on your data, you should compile these *mex* functions for your system.
This requires working compilers (e.g., gcc, g++) to be installed on your system, which can be configured using `mex -setup` (cf. `doc mex` for more information).

Once mex is set up, the mex functions used in the time-series code repository can be compiled by navigating to the **Toolboxes** directory and then running `compile_mex`.

## Compiling the *TISEAN* binaries

Some operations rely on the [*TISEAN* nonlinear time-series analysis package](http://www.mpipks-dresden.mpg.de/~tisean/Tisean_3.0.1/index.html), which Matlab accesses via the terminal using `system` commands, so the *TISEAN* binaries ***cannot*** be installed from within Matlab, but instead must be installed from the command line.
If you are running Linux or Mac, we will assume that you are familiar with the command line, while those running Windows will require an alternate method to install *TISEAN*, as explained below.

### Installing *TISEAN* on Linux or Mac

In the command line (**not within Matlab**), navigate to the **Toolboxes/Tisean_3.0.1** directory of the repository, then run the following chain of commands:

```bash
$ ./configure
$ make clean
$ make
$ make install
```

This should install the *TISEAN* binaries in your **~/bin/** directory (you can instead install into a system-wide directory, **/usr/bin**, for example, by running `./configure –prefix=/usr`). Additional information about the *TISEAN* installation process is provided [on the *TISEAN* website](http://www.mpipks-dresden.mpg.de/~tisean/Tisean_3.0.1/index.html).

If installation was successful then you should be able to access the newly-compiled binaries from the commandline, e.g., typing the command `which poincare` should return the path to the *TISEAN* function `poincare`.
Otherwise, you should check that the install directory is in your system path, e.g., by adding the following:

        export PATH=$PATH:$HOME/bin

to your **~/.bash_profile** (and running `source ~/.bash_profile` to update).

The path where *TISEAN* is installed will also have to be in Matlab’s environment path, which is added by `startup.m`, assuming that the binaries are stored in **~/bin**.
The `startup.m` code also adds the **DYLD_LIBRARY_PATH**, which is also required for *TISEAN* to function properly.

If you choose to use a custom location for the *TISEAN* binaries, that is not in the default Matlab system path (`getenv('PATH')` in Matlab), then you will have to add this path manually.
You can test that Matlab can see the *TISEAN* binaries by typing, for example, the following into Matlab:

    >> !which poincare

If Matlab’s system paths are set up correctly, this command should return the path to your compiled *TISEAN* binary, `poincare`.

### Installing *TISEAN* on Windows

If you are running Matlab from Windows, you will need a mechanism for Matlab to call `system` commands and find compiled TISEAN binaries.
There are two options:

1. **Install [Cygwin](http://www.cygwin.com) on your machine**. Cygwin provides a Linux distribution-like environment on Windows.
Use this environment to compile and install TISEAN (as per the instructions above for Linux or Mac), which will require it to have [C and fortran compilers installed](http://preshing.com/20141108/how-to-install-the-latest-gcc-on-windows/).
Matlab will then also need to be launched from Cygwin, using the command: `matlab &`.
This instance of Matlab should then be able to call `system` commands through cygwin, including the ability to access the *TISEAN* binaries.

2. **Sacrifice operations that rely on *TISEAN***.
In total, *TISEAN*-based operations account for approximately 300 operations in the operation library.
Although they provide important, well-tested implementations of nonlinear time-series analysis methods, it's not the end of the world if you decide it's too much trouble to install and are ok to miss out on these methods (see below on how to explicitly remove them from a computed library).

### Ignoring *TISEAN* functions

If you decide not to use functions from the *TISEAN* package, you should initialize your dataset with the TISEAN functions removed.
You could do this by removing them from you `INP_ops_hctsa.txt` file when initializing your dataset, or you could remove them from your initialized _hctsa_ dataset by filtering on the `'tisean'` keyword.

For example, to filter a local Matlab _hctsa_ file (e.g., `HCTSA.mat`), you can use the following: `TS_LocalClearRemove('raw','ops',TS_GetIDs('tisean','raw','ops'),true);`, which will remove all operations with the 'tisean' keyword from the _hctsa_ dataset in `HCTSA.mat`.

[If you are using a *mySQL* database to store the results of your _hctsa_ calculations, TISEAN functions can be removed from the database as follows: `SQL_ClearRemove('ops',SQL_GetIDs('ops',0,'tisean',{}),true)`].
