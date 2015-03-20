## Calculating {#sec:calculating}

In this chapter we describe our Matlab framework for computing the output of operations in the **Operations** table of the *mySQL* database on time series in the **TimeSeries** table of the database.
The procedure involves three main steps:

1.  Retrieve a set of time series and operations from (the **Results** table) of the database to a local Matlab file, `HCTSA_loc` (use `TSQ_prepared`).

2.  Compute the operations on the retrieved time series in Matlab and storing the results locally (use `TSQ_brawn`).

3.  Write the results back to the **Results** table of the database (use `TSQ_agglomerate`).

The process is represented schematically below.

![**Computation workflow schematic.**The three steps involved in computing time-series analysis operations on a set of time series are labeled: **1**. `TSQ_prepared` (retrieve data, including missing entries, from the database), **2**. `TSQ_brawn` (compute missing time series/operation pairs in HCTSA_loc.mat), **3**. `TSQ_agglomerate` (store the new results back in the database).](ComputationSchematic.png)

A sample script to iterate over these steps is the `sample_runscript` in the **Calculation** directory of the repository.
Additional details of the steps involved are provided below.


### Retrieving data from the database using `TSQ_prepared` {#sec:tsq_prepared}

Retrieving data from the **Results** table of the database is typically done for one of two purposes:

1. To calculate as-yet uncalculated entries to be stored back into the database, and
2. To analyze already-computed data stored in the database in Matlab.

The function `TSQ_prepared` performs both of these functions, using different inputs.
Here we describe the use of the `TSQ_prepared` function for the purposes of populating uncalculated (NULL) entries in the **Results** table of the database in Matlab.

For calculating missing entries in the database, `TSQ_prepared` can be run as follows:

    TSQ_prepared(ts_ids, op_ids, 'null');

The third input, `'null'`, retrieves `ts_id`s and `op_id`s from the sets provided that contain (as-yet) uncalculated (i.e., NULL) elements in the database; these can then be calculated and stored back in the database.
An example usage is given below:

    TSQ_prepared([1,3], 1:500, 'null');

Running this code will retrieve data for time series with `ts_id`s 1 and 3 and operations with `op_id` in the range 1 to 500, keeping only the rows and columns of the resulting time series $$\times$$ operations matrix that contain NULLs.
When calculations are complete and one wishes to analyze all of the data stored in the database (not just NULL entries requiring computation), the third input should be set to ‘all’ to retrieve all entries in the **Results** table of the database.

The result of running `TSQ_prepared` is a local Matlab file, `HCTSA_loc.mat`, that contains the relevant data retrieved from the
database. The file contains the following elements:

-   **TS_DataMat** is an *n* $$\times$$ *m* matrix corresponding to the *n* time series and *m* operations retrieved from the database. Elements of **TS\_DataMat** correspond to the result of applying each operation to each time series.

-   **TS_Quality** is an *n* $$\times$$ *m* matrix containing quality labels for each operation output. Quality labels are shown in Tab.[tab:qualityLabels].

-   **TS_CalcTimes** is an *n* $$\times$$ *m* matrix containing calculation times for each operation output. Note that for operations that point to a structure produced by a master operation operations, the calculation time stored is that taken to compute the entire master function from which they were derived.

-   **TimeSeries** is a structure array containing metadata about the time series retrieved (corresponding to rows of the **TS_** matrices).

-   **Operations** is a structure array containing metadata about the operations retrieved (columns of the **TS_** matrices).

-   **MasterOperations** is a structure array containing metadata about the master operations retrieved, corresponding to the code evaluated that is referenced by each operation.

Note that NULL entries in the database are converted to NaN entries in the local Matlab matrices.

**Quality labels** are stored in the **Quality** column of the **Results** table in the *mySQL* database (and locally in **TS_Quality** matrix).
Values are used to indicate non-real-valued outputs from operations, or cases when fatal errors were encountered.
Note that when the quality label is nonzero (i.e., the quality encodes a special-valued output), the actual output value of the operation is set to zero, as a convention:

| **Quality label** | **Description** |
|:-------------:|:-------------:|
| 0 | No problems with calculation. Output was a real number. |
| 1 | When running the code, a fatal error was encountered. |
| 2 | Output of the code was `NaN`.|
| 3 | Output of the code was `Inf`. |
| 4 | Output of the code was `-Inf` |
| 5 | Output had a nonzero imaginary component |
|:-------------:|:-------------:|

<!-- **Quality labels**. These are stored in the **Quality** column of
  the **Results** table in the *mySQL* database (and
  locally in **TS_Quality** matrix). Values are used to indicate
  non-real-valued outputs from operations, or cases when fatal errors
  were encountered. Note that when the quality label is nonzero (i.e.,
  the quality encodes a special-valued output), the actual output value
  of the operation is set to zero, as a convention.<span
  data-label="tab:qualitylabels"></span> -->

### Performing calculations using `TSQ_brawn` {#sec:performing_calculations}

Once a relevant section of the data matrix has been retrieved, we can calculate values that have not previously been calculated, indicated by NULL entries in the **Results** table of the database, and NaN entries in the corresponding `HCTSA_loc.mat`.

Calculations are performed using the function `TSQ_brawn`, which stores results back into the matrices in `HCTSA_loc`. This function is generally run without inputs:

        TSQ_brawn;

Inputs to the function are optional and can be used to specify whether to log to file or the screen (‘tolog’, default: no), and whether to compute operations across cores using the Parallel processing (‘toparallel’, default: no). Running `TSQ_brawn` will begin running operations on time series in `HCTSA_loc.mat` for which elements in **TS\_DataMat** are NaNs (indicating that they have not been run
before), and storing the results back in the matrices of `HCTSA_loc.mat`, i.e., **TS\_DataMat** (output of each operation on each time series), **TS\_CalcTime** (calculation time for each operation on each time series), and **TS\_Quality** (labels indicating errors or special-valued outputs). When all NULL entries in **TS\_DataMat** have been calculated, **TSQ_brawn** saves the results back to the local file: `HCTSA_loc.mat`.
These results can then be written back to the database using `TSQ_agglomerate`, as shown in Fig.[fig:ComputationSchematic].

### Writing calculations back to the database using `TSQ_agglomerate` {#sec:writingCalcsDatabase}

Once calculations have been performed using Matlab on local files, the results must be written back to the database.
This task is performed by `TSQ_agglomerate`, which reads the data in `HCTSA_loc.mat`, checks that the metadata still matches the database, and then begins updating the **Output**, **Quality**, and **CalculationTime** columns of the **Results** table in the *mySQL* database.
This can be done by simply running:

        TSQ_agglomerate;

### Cycling through calculations using runscripts {#sec:runscripts}

This section on computation has outlined the three main steps to computation as:

1. Retrieving from the database (`TSQ_prepared`),
2. Calculating operations on time series (`TSQ_brawn`), and
3. Writing the results back to the database (`TSQ_agglomerate`).

In order to calculate across large sections of the database, it is convenient to use runscripts, that allow large computations to be broken up into smaller pieces, and using `for` loops to cycle through these pieces.
By designating different sections of the database to cycle through, this procedure can also be used to (manually) distribute the computation across different machines.
Retrieving a large section of the database at once can be problematic because it requires large disk reads and writes, uses a lot of memory, and if problems occur in the reading or writing to/from files, one may have to abandon a large number of existing computations.
Due to the ability of `TSQ_brawn` to parallelize the computation across multiple CPU processors, it is usually the most efficient practice to retrieve a small number of time series at each iteration of the prepared–brawn–agglomerate loop.
Depending on their length, we usually retrieve five time series at each iteration of a runscript loop.
An example runscript is given in the code that accompanies this document, as `sample_runscript.m`, and can be viewed as a template for runscripts that one may wish to use when performing time-series calculations across the database.

### Dealing with errors {#sec:errors}

Errors in particular pieces of code can often occur when applying them to large numbers of time series. Some errors are not problems with the code, but problems with applying particular sets of code to particular time series, such as when a Matlab fitting function reaches the maximum number of iterations and returns an error.
These are kept and stored as errors.
Other errors are genuine problems with the code that need to be corrected.
Although we have attempted to preempt and deal with as many errors in the code as possible, ongoing feedback will be used to further refine and develop the code over time.
Note that errors returned from Matlab files are dealt with using `try-catch` statements, but errors with `mex` functions can produce a fault that crashes Matlab or the system.
These situations must be dealt with by either identifying and fixing the problem in the original source code and recompiling, or by removing the problem code.