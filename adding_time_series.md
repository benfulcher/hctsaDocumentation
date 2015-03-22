## Adding time series to the database
<!--{#sec:addingTimeSeries}-->

The most common task is adding time series to the database, which must be done for each dataset analyzed.
It is up to personal preference of the user whether to keep all time-series data in a single database, or have a different database for each dataset.

Time series are added using the same function used to add master operations and operations to the database, `SQL_add`.
The input file to this function should specify a set of time-series data files and keywords to assign to them, as explained below.

Every time series added to the database will be given a unique integer identifier, **ts\_id**, which is used to retrieve specific time series from the database.

### Example: Adding time series to the database
Adding a set of time series to the database requires an appropriately formatted input file, **INP_ts.txt**, for example, the appropriate code is:

    SQL_add('ts','INP_ts.txt');

We provide an example input file in the **Database** directory as **INP_test_ts.txt**, which can be added to the database, following the syntax above, using `SQL_add('ts','INP_test_ts.txt')`.

### Input file format

The `SQL_add` function expects the input text file to be formatted with each row specifying the file name of a time series and comma-delimited keywords with white space between them.
For example, consider the following input file, containing three lines (one for each time series to be added to the database):

    gaussianwhitenoise_001.dat     noise,gaussian
    gaussianwhitenoise_002.dat     noise,gaussian
    sinusoid_001.dat               periodic,sine
    
Running `SQL_add` with this input file will add three time series to the database. The time series stored in the files **gaussianwhitenoise_001.dat** and **gaussianwhitenoise_002.dat** will be assigned the keywords ‘noise’ and ‘gaussian’, and the time series stored in the file **sinusoid_001.dat** will be added with keywords ‘periodic’ and ‘sine’.

`SQL_add` will attempt to find the time-series data files given in the input file, read them (using `dlmread`), and then import all of this data into the database.
Once imported, the time-series data is stored in the database, and the original files in the input file no longer need to kept in the Matlab path.
If keywords are provided in the input file, the time series are indexed and then updated in the **TimeSeriesKeywords** table and associated index table, **TsKeywordsRelate**.