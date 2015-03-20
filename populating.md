## Populating the database with time series and operations {#sec:PopulatingDatabase}

Adding time series or operations to the database is done using the function `SQL_add`.
It has two key inputs that specify:

1. Whether to import a set of time series (specify `‘ts’`), a set of operations (specify `‘ops’`), or a set of master operations (specify `‘mops’`),
2. The name of the input text file that contains appropriately-formatted information about the time series, master operations, or operations.

Note that manually adding or deleting rows to the database can create inconsistencies and errors in the database structure and should not be done unless you *really* know what you’re doing.
Adding time series and operations to the database should always be done using `SQL_add` which sets up the **Results** table of the database and ensures that the indexing relationships in the database
are properly maintained.
We deal with adding [master operations](#sec:addingMops), [operations](#sec:addingOps), and [time series](#sec:addingTimeSeries) separately.
Users wishing to run the existing library of code on their own set of time series will only need to add time series, as the full operation library is added in the `install.m` script.

### Adding master operations to the database {#sec:addingMops}

By default, the `install` script populates the database with the default library of highly comparative time-series analysis code.
The formatted input file specifying these pieces of code and their input parameters is **INP_mops.txt** in the **Database** directory of the repository.
These operations can be added to the database using the following command:

    SQL_add('mops','INP_mops.txt');

Two example lines from the input file, ‘INP_mops.txt’, are as follows:

    CO_tc3(y,1)     CO_tc3_y_1
    ST_length(x)    ST_length

Each line in the input file specifies a piece of code and its input parameters as well as a unique name for that master operation, separated by whitespace; this name is referenced by individual operations.
Note that we use the convention that *x* refers to the input time series and *y* refers to the *z*-scored input time series.
In the example above, the first line thus adds an entry in the database for running the code `CO_tc3` using a *z*-scored time series as input, with ‘1’ as the second input with the label
`CO_tc3_y_1`, and the second line will add an entry for running the code `ST_length` on a time series input, using the label `length`.
The output of all master operations should either be a real number, a structure, or a ‘NaN’ to indicate that the input time series is not appropriate for this code.
The operations make up elements of the data matrix.

All pieces of code should be accessible in the current Matlab path.
When the time comes to perform computations on data using the methods in the database, we assume that Matlab has access to each of the functions specified by their code name in the database.
For the above example, this means that we assume Matlab has access to the function `CO_tc3` (because it will attempt to run `CO_tc3(y,1)`), and also the function `ST_length(x)`.

### Adding operations to the database {#sec:addingOps}

Operations can be added to the mySQL database using the following:

    SQL_add('ops','INP_ops.txt');

The input file, e.g., `INP_ops.txt` (in the **Database** directory of the repository) should contain a row for every operation, and use labels that correspond to master operations.
An example excerpt from such a file is below:

    CO_tc3_y_1.raw     CO_tc3_1_raw      correlation,nonlinear
    CO_tc3_y_1.abs     CO_tc3_1_abs      correlation,nonlinear
    CO_tc3_y_1.num     CO_tc3_1_num      correlation,nonlinear
    CO_tc3_y_1.absnum  CO_tc3_1_absnum   correlation,nonlinear
    CO_tc3_y_1.denom   CO_tc3_1_denom    correlation,nonlinear
    ST_length          length            raw,lengthDependent

The first column references a corresponding master label and, in the case of master operations that produce structure, the particular field of the structure to reference (after the fullstop), the second column denotes the label for the operation, and the final column is a set of comma-delimited keywords (that must not include whitespace).
White space is used to separate the three entries on each line of the input file.
In this example, the master operation labeled `CO_tc3_y_1`, outputs is a structure, with fields that are referenced by the first five operations listed here, and the `ST_length` master operation outputs a single number (the length of the time series), which is referenced by the operation named **'length'** here.
The two keywords 'correlation' and 'nonlinear' are added to the `CO_tc3` operations, while the keywords ‘raw’ and ‘lengthDependent’ are added to the `ST_length` operation.
These keywords can be used to organize and filter the set of operations used for a given analysis task.

Every operation added to the database will be given a unique integer identifier, **op_id**, which provides a common way of retrieving specific operations from the database.
Similarly, every master operation is given a unique integer, **mop_id**, that can be used to identify it.

Note that after (hopefully successfully) adding the operations to the database, the `SQL_add` function indexes the operation keywords to an **OperationKeywords** table that produces a unique identifier for each keyword, and another linking table that allows operations with each keyword to be retrieved efficiently.

### Adding time series to the database {#sec:addingTimeSeries}

The most common task is adding time series to the database, which must be done for each dataset analyzed. It is up to personal preference of the user whether to keep all time-series data in a single database, or
have a different database for each dataset.
Adding a set of time series to the database is done as above, and for a given input file with filename `INP_ts.txt`, for example, the appropriate code is:

    SQL_add('ts','INP_ts.txt');

We provide an example input file in the **Database** directory as
`INP_test_ts.txt`.
The `SQL_add` function expects the text file to be formatted with each row specifying the filename of a time series and comma-delimited keywords with whitespace between them.
For example, the following input file would add two time series named **gaussianwhitenoise_001.dat** and **gaussianwhitenoise_002.dat** to the database, each with keywords **‘noise’** and **‘gaussian’**, and it would then add the time series stored in the file `sinusoid_001.dat` with keywords ‘periodic’ and ‘sine’:

    gaussianwhitenoise_001.dat     noise,gaussian
    gaussianwhitenoise_002.dat     noise,gaussian
    sinusoid_001.dat               periodic,sine

Every time series added to the database will be given a unique integer identifier, `ts_id`, which is used to retrieve specific time series from the database.
`SQL_add` will attempt to find the time-series data files given in the input file, read them (using `dlmread`), and then import all of this data into the database.
Once imported, the time-series data is stored in the database, and the original files in the input file no longer need to kept in the Matlab path.
If keywords are provided in the input file, the time series are indexed and then updated in the **TimeSeriesKeywords** table and associated index table.