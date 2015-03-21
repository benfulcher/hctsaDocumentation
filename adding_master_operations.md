# Adding master operations

### Adding master operations to the database
<!--{#sec:addingMops}-->

By default, the `install` script populates the database with the default library of highly comparative time-series analysis code.
The formatted input file specifying these pieces of code and their input parameters is **INP_mops.txt** in the **Database** directory of the repository.
These operations can be added to the database using the following command:

    SQL_add('mops','INP_mops.txt');

Two example lines from the input file, **INP_mops.txt**, are as follows:

    CO_tc3(y,1)     CO_tc3_y_1
    ST_length(x)    ST_length

Each line in the input file specifies a piece of code and its input parameters as well as a unique name for that master operation, separated by whitespace; this name is referenced by individual operations.
Note that we use the convention that *x* refers to the input time series and *y* refers to the *z*-scored input time series.
In the example above, the first line thus adds an entry in the database for running the code `CO_tc3` using a *z*-scored time series as input (*y*), with ‘1’ as the second input with the label **CO_tc3_y_1**, and the second line will add an entry for running the code `ST_length` using an unprocessed time-series input *x*, using the label **length**.
The output of all master operations should either be a real number, a structure, or a ‘NaN’ to indicate that the input time series is not appropriate for this code.
The operations make up elements of the data matrix.

All pieces of code should be accessible in the current Matlab path.
When the time comes to perform computations on data using the methods in the database, we assume that Matlab has access to each of the functions specified by their code name in the database.
For the above example, this means that we assume Matlab has access to the function `CO_tc3` (because it will attempt to run `CO_tc3(y,1)`), and also the function `ST_length(x)`.