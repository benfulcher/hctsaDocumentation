# Populating the database with time series and operations

When linking Matlab to a _mySQL_ database, metadata associated with time series, operations, and master operations, as well as the results of computations are all stored in an indexed database. Adding master operations, operations, and time series to the database can be achieved using the `SQL_Add` function, as described below.

The following table summarizes the terminology used for each type of object in _hctsa_ land:

|                          | **Master Operation** | **Operation** | **Time Series** |
| :----------------------: | :------------------: | :-----------: | --------------- |
| **Database identifier**: |        mop\_id       |     op\_id    | ts\_id          |
|  **Input to** `SQL_Add`: |        'mops'        |     'ops'     | 'ts'            |

## Using `SQL_Add`

`SQL_Add` has two key inputs that specify:

1. Whether to import a set of time series (specify `‘ts’`), a set of operations (specify `‘ops’`), or a set of master operations (specify `‘mops’`),
2. The name of the input file that contains [appropriately-formatted information](../calculating/input\_files.md) about the time series, master operations, or operations to be imported.

In this section, we describe how to use `SQL_Add` to add master operations, operations, and time series to the database.

Users wishing to run the default _hctsa_ code library their own time-series dataset will only need to add time series to the database, as the full operation library is added by default by the `install.m` script. Users wishing to add additional features using custom time-series code or different types of inputs to existing code files, can either edit the default _INP\_ops.txt_ and _INP\_mops.txt_ files provided with the repository, or create new input files for their custom analysis methods (as explained for [operations](https://github.com/benfulcher/hctsaDocumentation/tree/71794292cac125d96004eacd0c1934c6feacd36b/adding\_operations.md) and [master operations](https://github.com/benfulcher/hctsaDocumentation/tree/71794292cac125d96004eacd0c1934c6feacd36b/adding\_master\_operations.md)).

_**REMINDER**_: Manually editing the database, including adding or deleting rows, is very dangerous, as it can create inconsistencies and errors in the database structure. Adding time series and operations to the database should only be done using `SQL_Add` which sets up the **Results** table of the database and ensures that the indexing relationships in the database are properly maintained.

### _Example_: Adding our library of master operations to the database

By default, the `install` script populates the database with the default library of highly comparative time-series analysis code. The formatted input file specifying these pieces of code and their input parameters is **INP\_mops.txt** in the **Database** directory of the repository. This step can be reproduced using the following command:

```
SQL_Add('mops','INP_mops.txt');
```

Once added, each master operation is assigned a unique integer, **mop\_id**, that can be used to identify it. For example, when adding individual operations, the **mop\_id** is used to map each individual operation to a corresponding master operation.

#### Adding new pieces of executable code to the database

New functions and their input parameters to execute can be added to the database using `SQL_Add` in the same way as described above. For example, lines corresponding to the new code can be added to the current **INP\_mops.txt** file, or by generating a new input file and running `SQL_Add` on the new input file. Once in the database, the software will then run the new pieces of code. Note that `SQL_Add` checks for repeats that already exist in the database, so that duplicate database entries cannot be added with `SQL_Add`.

New code added to the database should be checked for the following: 1. Output is a real number or structure (and uses an output of NaN to assign all outputs to a NaN). 2. The function is accessible in the Matlab path. 3. Output(s) from the function have matching operations (or features), which also need to be [added to the database](https://github.com/benfulcher/hctsaDocumentation/tree/71794292cac125d96004eacd0c1934c6feacd36b/adding\_operations.md).

Corresponding operations (or features) will then need to added separately, to link to the structured outputs of master operations.

### _Example_: Adding our library of operations to the database

Operations can be added to the _mySQL_ database using an [appropriately-formatted input file](../calculating/input\_files.md), such as `INP_ops.txt`, as follows:

```
SQL_Add('ops','INP_ops.txt');
```

Every operation added to the database will be given a unique integer identifier, **op\_id**, which provides a common way of retrieving specific operations from the database.

Note that after (hopefully successfully) adding the operations to the database, the `SQL_Add` function indexes the operation keywords to an **OperationKeywords** table that produces a unique identifier for each keyword, and another linking table that allows operations with each keyword to be retrieved efficiently.

### Adding time series to the database

After setting up a database with a library of time-series features, the next task is to add a dataset of time series to the database. It is up to the user whether to keep all time-series data in a single database, or have a different database for each dataset.

Time series are added using the same function used to add master operations and operations to the database, `SQL_Add`, which imports time series data (stored in time-series data files) and associated keyword metadata (assigned to each time series) to the database.

Time series can be indexed by assigning keywords to them (which are stored in the **TimeSeriesKeywords** table and associated index table, **TsKeywordsRelate** of the database).

When added to the _mySQL_ database, every time series added to the database is assigned a unique integer identifier, **ts\_id**, which can be used to retrieve specific time series from the database.

Adding a set of time series to the database requires an [appropriately formatted input file](../calculating/input\_files.md), following either of the following:

```
% Add time series (embedded in a .mat file):
SQL_Add('ts','INP_ts.mat');

% Add time series (stored in data files) using an input text file:
SQL_Add('ts','INP_ts.txt');
```

We provide an example input file in the **Database** directory as **INP\_test\_ts.txt**, which can be added to the database, following the syntax above, using `SQL_Add('ts','INP_test_ts.txt')`, as well as a sample .mat file input as **INP\_test\_ts.mat**, which can be added as `SQL_Add('ts','INP_test_ts.mat')`.
