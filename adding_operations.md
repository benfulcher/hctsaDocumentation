## Adding operations to the database
<!--{#sec:addingOps}-->


Operations can be added to the *mySQL* database using the following:

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