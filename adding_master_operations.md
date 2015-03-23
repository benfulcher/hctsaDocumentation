# Adding master operations to the database

In our system, a *master operation* refers to a piece of Matlab code and a set of input parameters, e.g., `CO_AutoCorr(x)`, which runs the `CO_AutoCorr.m` function on an input time series, x.
A valid output from a master operation is: (i) a real number, (ii) a structure containing real numbers, or (iii) `NaN` to indicate that the input time series is not appropriate for this code.
Each operation (or feature) corresponds to a single real number, making up a column of the resulting data matrix, and can be one of many outputs from a master operation.
To add new master operations to the database of code, you need to create an input file and use `SQL_add`.
Once in the database, the software will then run the new pieces of code.
Corresponding operations (or features) will then need to added separately, to link to the structured outputs of master operations.
Every master operation added to the database is assigned a unique integer, **mop_id**, that can be used to identify it.


## *Example*: Add our library of master operations to the database
<!--{#sec:addingMops}-->

By default, the `install` script populates the database with the default library of highly comparative time-series analysis code.
The formatted input file specifying these pieces of code and their input parameters is **INP_mops.txt** in the **Database** directory of the repository.
This step can be reproduced using the following command:

    SQL_add('mops','INP_mops.txt');

Two example lines from the input file, **INP_mops.txt**, are as follows:

    CO_tc3(y,1)     CO_tc3_y_1
    ST_length(x)    ST_length

Each line in the input file specifies (i) a piece of code and its input parameters as well as a unique name for that master operation, separated by whitespace; this name is referenced by individual operations.
Note that we use the convention that *x* refers to the input time series and *y* refers to the *z*-scored input time series.
In the example above, the first line thus adds an entry in the database for running the code `CO_tc3` using a *z*-scored time series as input (*y*), with ‘1’ as the second input with the label **CO_tc3_y_1**, and the second line will add an entry for running the code `ST_length` using the non-*z*-scored time-series *x*, with the label **length**.

When the time comes to perform computations on data using the methods in the database, Matlab needs to have path access to each of the master operations functions specified in the database.
For the above example, this means that the functions `CO_tc3` and `ST_length` are in the Matlab path (because it will attempt to run `CO_tc3(y,1)` and `ST_length(x)`).
The script `startup.m` handles the addition of paths required for the code library.

## Adding new pieces of executable code to the database

New functions and their input parameters to execute can be added to the database in the same way as described above.
For example, lines corresponding to the new code can be added to the current **INP_mops.txt** file, or by generating a new input file and running `SQL_add` on the new input file.
Note that `SQL_add` checks for repeats that already exist in the database, so there is no need to worry about generating duplicate database entries with `SQL_add`.
New code added to the database should be checked for the following:
1. Output is a real number or structure (and uses an output of NaN to assign all outputs to a NaN).
2. The function is accessible in the Matlab path.
3. Corresponding outputs from the function (in the case of a structure) have matching operations (or features), which also need to be [added to the database](adding_operations.md).