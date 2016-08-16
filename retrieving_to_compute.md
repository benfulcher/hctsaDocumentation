# Retrieving data from the database using `SQL_retrieve`
<!--{#sec:tsq_prepared}-->

Retrieving data from the **Results** table of the database is typically done for one of two purposes:

1. To calculate as-yet uncalculated entries to be stored back into the database, and
2. To analyze already-computed data stored in the database in Matlab.

The function `SQL_retrieve` performs both of these functions, using different inputs.
Here we describe the use of the `SQL_retrieve` function for the purposes of populating uncalculated (NULL) entries in the **Results** table of the database in Matlab.

For calculating missing entries in the database, `SQL_retrieve` can be run as follows:

        SQL_retrieve(ts_ids, op_ids, 'null');

The third input, `'null'`, retrieves **ts_id**s and **op_id**s from the sets provided that contain (as-yet) uncalculated (i.e., NULL) elements in the database; these can then be calculated and stored back in the database.
An example usage is given below:

        SQL_retrieve(1:5, 'all', 'null');

Running this code will retrieve null (uncalculated) data from the database for time series with **ts_id**s between 1 and 5 (inclusive) and all operations in the database, keeping only the rows and columns of the resulting time series x operations matrix that contain NULLs.

When calculations are complete and one wishes to analyze all of the data stored in the database (not just NULL entries requiring computation), the third input should be set to ‘all’ to retrieve all entries in the **Results** table of the database, as described [later](retrieving.md).

`SQL_retrieve` writes to a local Matlab file, **HCTSA.mat**, that contains the data retrieved from the database.

<!-- **Quality labels**. These are stored in the **Quality** column of
  the **Results** table in the *mySQL* database (and
  locally in **TS_Quality** matrix). Values are used to indicate
  non-real-valued outputs from operations, or cases when fatal errors
  were encountered. Note that when the quality label is nonzero (i.e.,
  the quality encodes a special-valued output), the actual output value
  of the operation is set to zero, as a convention.<span
  data-label="tab:qualitylabels"></span> -->
