### Adding time series to the database {#sec:addingTimeSeries}

Every time series added to the database will be given a unique integer identifier, **ts\_id**, which is used to retrieve specific time series from the database.

The most common task is adding time series to the database, which must be done for each dataset analyzed. It is up to personal preference of the user whether to keep all time-series data in a single database, or have a different database for each dataset.


###
Adding a set of time series to the database is done as above, and for a given input file with filename `INP_ts.txt`, for example, the appropriate code is:

    SQL_add('ts','INP_ts.txt');

We provide an example input file in the **Database** directory as `INP_test_ts.txt`.
The `SQL_add` function expects the text file to be formatted with each row specifying the file name of a time series and comma-delimited keywords with whitespace between them.
For example, the following input file would add two time series named **gaussianwhitenoise_001.dat** and **gaussianwhitenoise_002.dat** to the database, each with keywords ‘noise’ and ‘gaussian’, and it would then add the time series stored in the file **sinusoid_001.dat** with keywords ‘periodic’ and ‘sine’:

    gaussianwhitenoise_001.dat     noise,gaussian
    gaussianwhitenoise_002.dat     noise,gaussian
    sinusoid_001.dat               periodic,sine

`SQL_add` will attempt to find the time-series data files given in the input file, read them (using `dlmread`), and then import all of this data into the database.
Once imported, the time-series data is stored in the database, and the original files in the input file no longer need to kept in the Matlab path.
If keywords are provided in the input file, the time series are indexed and then updated in the **TimeSeriesKeywords** table and associated index table.