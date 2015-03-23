# Adding master operations to the database

In our system, a *master operation* refers to a piece of Matlab code and a set of input parameters, e.g., `CO_AutoCorr(x)`, which runs the `CO_AutoCorr` function on an input time series, *x*.

Valid outputs from a master operation are:
1. A real number,
2. A structure containing real numbers,
3. **NaN** to indicate that the input time series is not appropriate for this code.

The (potentially many) outputs from a master operation can thus be mapped to individual operations (or features), which are single real numbers summarizing a time series that make up individual columns of the resulting data matrix.

## *Example*: Adding our library of master operations to the database
<!--{#sec:addingMops}-->

By default, the `install` script populates the database with the default library of highly comparative time-series analysis code.
The formatted input file specifying these pieces of code and their input parameters is **INP_mops.txt** in the **Database** directory of the repository.
This step can be reproduced using the following command:

    SQL_add('mops','INP_mops.txt');

Two example lines from the input file, **INP_mops.txt**, are as follows:

    CO_tc3(y,1)     CO_tc3_y_1
    ST_length(x)    ST_length

Each line in the input file specifies two pieces of information, separated by whitespace:
1. A piece of code and its input parameters.
2. A unique label for that master operation (that can be referenced by [individual operations](adding_operations.md)).

We use the convention that *x* refers to the input time series and *y* refers to a *z*-scored transformation of the input time series (i.e., $$(x - \mu_x)/\sigma_x$$).
In the example above, the first line thus adds an entry in the database for running the code `CO_tc3` using a *z*-scored time series as input (*y*), with ‘1’ as the second input with the label **CO_tc3_y_1**, and the second line will add an entry for running the code `ST_length` using the non-*z*-scored time-series *x*, with the label **length**.

When the time comes to perform computations on data using the methods in the database, Matlab needs to have path access to each of the master operations functions specified in the database.
For the above example, Matlab will attempt to run both `CO_tc3(y,1)` and `ST_length(x)`, and thus the functions `CO_tc3.m` and `ST_length.m` must be in the Matlab path.
Recall that the script `startup.m`, which should be run at the start of each session using *hctsa*, handles the addition of paths required for the default code library.

Once added, each master operation is assigned a unique integer, **mop_id**, that can be used to identify it.
For example, when adding individual operations, the **mop_id** is used to map each individual operation to a corresponding master operation.

## Adding new pieces of executable code to the database

New functions and their input parameters to execute can be added to the database using `SQL_add` in the same way as described above.
For example, lines corresponding to the new code can be added to the current **INP_mops.txt** file, or by generating a new input file and running `SQL_add` on the new input file.
Once in the database, the software will then run the new pieces of code.
Note that `SQL_add` checks for repeats that already exist in the database, so that duplicate database entries cannot be added with `SQL_add`.

New code added to the database should be checked for the following:
1. Output is a real number or structure (and uses an output of NaN to assign all outputs to a NaN).
2. The function is accessible in the Matlab path.
3. Output(s) from the function have matching operations (or features), which also need to be [added to the database](adding_operations.md).

<!--Corresponding operations (or features) will then need to added separately, to link to the structured outputs of master operations.-->