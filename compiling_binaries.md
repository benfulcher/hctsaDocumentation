# Compiling binaries

Some external code packages require compiled binary code to be used.
Compilation of the mex code is handled as part of the `install.m` script, but the *TISEAN* package binaries will need to be compiled separately in the commandline.

## Compiling mex code
<!--{#sec:CompilingMexCode}-->

Many of the operations (especially external code packages) rely on mex functions, pieces of code not written in Matlab, that need to be compiled to run natively on each system architecture.
To ensure that as many operations as possible run successfully on your data, you should compile these mex functions for your system.
This requires working compilers (e.g., gcc, g++) to be installed on your system, which can be configured using `mex -setup` (cf. `doc mex` for more information).
Once mex is set up, the mex functions used in the time-series code repository can be compiled by navigating to the **Toolboxes** directory and then running `compile_mex`.

## Compiling the TISEAN binaries
<!--{#sec:CompilingTisean}-->

Some operations rely on the [*TISEAN* nonlinear time-series analysis package](http://www.mpipks-dresden.mpg.de/~tisean/Tisean_3.0.1/index.html), which Matlab accesses via `system` commands, so the *TISEAN* binaries ***cannot*** be installed from within Matlab, but instead must be installed from the commandline.
From the **Toolboxes/Tisean_3.0.1** directory of the repository, run the following chain of commands:

        ./configure
        make clean
        make
        make install

This should install the *TISEAN* binaries in your **~/bin/** directory (you can instead install into a system-wide directory, **/usr/bin**, for example, by running `./configure –prefix=/usr/bin`).

You should be able to access these binaries from the commandline, e.g., typing the command `which poincare` should return the path to the *TISEAN* function `poincare`.
Otherwise, you should check that this directory is in your path, e.g., by adding

        export PATH=$PATH:$HOME/bin

to your **~/.bash_profile** (and running `source ~/.bash_profile` to update).

The path you install *TISEAN* to will also have to be in Matlab’s environment path, which is added by `startup.m`, and assumes that the binaries are stored in **~/bin**.
The `startup.m` code also adds the **DYLD_LIBRARY_PATH**, which is also required for *TISEAN* to function properly.

If you choose to use a custom location for the *TISEAN* binaries, that is not in the default Matlab system path (`getenv('PATH')` in Matlab) then you will have to add this path.
You can test that Matlab can see the *TISEAN* binaries by typing, for example, the following into Matlab:

        !which nstat_z

If Matlab’s system paths are set up correctly, this command should return the path to your compiled *TISEAN* binary, `nstat_z`.