# Retrieving, calculating, and writing


## Retrieving data from the database using `TSQ_prepared`
<!--{#sec:tsq_prepared}-->

Retrieving data from the **Results** table of the database is typically done for one of two purposes:

1. To calculate as-yet uncalculated entries to be stored back into the database, and
2. To analyze already-computed data stored in the database in Matlab.

The function `TSQ_prepared` performs both of these functions, using different inputs.
Here we describe the use of the `TSQ_prepared` function for the purposes of populating uncalculated (NULL) entries in the **Results** table of the database in Matlab.

For calculating missing entries in the database, `TSQ_prepared` can be run as follows:

        TSQ_prepared(ts_ids, op_ids, 'null');

The third input, `'null'`, retrieves **ts_id**s and **op_id**s from the sets provided that contain (as-yet) uncalculated (i.e., NULL) elements in the database; these can then be calculated and stored back in the database.
An example usage is given below:

        TSQ_prepared([1,3], 1:500, 'null');

Running this code will retrieve data for time series with **ts_id**s 1 and 3 and operations with **op_id**s in the range 1 to 500, keeping only the rows and columns of the resulting time series x operations matrix that contain NULLs.

When calculations are complete and one wishes to analyze all of the data stored in the database (not just NULL entries requiring computation), the third input should be set to ‘all’ to retrieve all entries in the **Results** table of the database, as described [later](retrieving.md).

`TSQ_prepared` writes to a local Matlab file, **HCTSA_loc.mat**, that contains the data retrieved from the database.
It contains the following elements:

-   **TS_DataMat** is an *n* x *m* matrix corresponding to the *n* time series and *m* operations retrieved from the database. Elements of **TS\_DataMat** correspond to the result of applying each operation to each time series.

-   **TS_Quality** is an *n* x *m* matrix containing quality labels for each operation output. Quality labels are described below.

-   **TS_CalcTimes** is an *n* x *m* matrix containing calculation times for each operation output. Note that for operations that point to a structure produced by a master operation operations, the calculation time stored is that taken to compute the entire master function from which they were derived.

-   **TimeSeries** is a structure array containing metadata about the time series retrieved (corresponding to rows of the **TS_** matrices).

-   **Operations** is a structure array containing metadata about the operations retrieved (columns of the **TS_** matrices).

-   **MasterOperations** is a structure array containing metadata about the master operations retrieved, corresponding to the code evaluated that is referenced by each operation.

NULL entries in the database are converted to NaN entries in the local Matlab matrices.

### Quality labels

*Quality labels* are used to indicate when operations take non-real values, or when fatal errors were encountered.
Quality labels are stored in the **Quality** column of the **Results** table in the *mySQL* database, and in local Matlab files as the **TS_Quality** matrix.

When the quality label is nonzero (i.e., the quality encodes a special-valued output), the actual output value of the operation is set to zero, as a convention:


| **Quality label** | **Description** |
|:-------------:|:-------------:|
| 0 | No problems with calculation. Output was a real number. |
| 1 | When running the code, a fatal error was encountered. |
| 2 | Output of the code was **NaN**.|
| 3 | Output of the code was **Inf**. |
| 4 | Output of the code was **-Inf** |
| 5 | Output had a non-zero imaginary component |

<!-- **Quality labels**. These are stored in the **Quality** column of
  the **Results** table in the *mySQL* database (and
  locally in **TS_Quality** matrix). Values are used to indicate
  non-real-valued outputs from operations, or cases when fatal errors
  were encountered. Note that when the quality label is nonzero (i.e.,
  the quality encodes a special-valued output), the actual output value
  of the operation is set to zero, as a convention.<span
  data-label="tab:qualitylabels"></span> -->