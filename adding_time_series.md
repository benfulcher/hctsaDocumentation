# Adding time series to the database
<!--{#sec:addingTimeSeries}-->

After setting up a database with a library of time-series features, the next task is to add a dataset of time series to the database.
It is up to personal preference of the user whether to keep all time-series data in a single database, or have a different database for each dataset.

Time series are added using the same function used to add master operations and operations to the database, `SQL_add`, which imports time series data (stored in time-series data files) and associated keyword metadata (assigned to each time series) to the database.
The time-series data files to import, and the keywords to assign to each time series are specified in a structured input file, as explained below.

Every time series added to the database is assigned a unique integer identifier, **ts\_id**, which can be used to retrieve specific time series from the database.

## *Example*: Adding a set of time series to the database
Adding a set of time series to the database requires an appropriately formatted input file, **INP_ts.txt**, for example, the appropriate code is:

    SQL_add('ts','INP_ts.txt');

We provide an example input file in the **Database** directory as **INP_test_ts.txt**, which can be added to the database, following the syntax above, using `SQL_add('ts','INP_test_ts.txt')`.

### Input file format

The `SQL_add` function expects the input text file to be formatted with each row specifying: (i) the file name of a time-series data file and (ii) comma-delimited keywords, with white space separating them.
For example, consider the following input file, containing three lines (one for each time series to be added to the database):

    gaussianwhitenoise_001.dat     noise,gaussian
    gaussianwhitenoise_002.dat     noise,gaussian
    sinusoid_001.dat               periodic,sine
    
Running `SQL_add` with this input file will add three time series to the database. The time series stored in the files **gaussianwhitenoise_001.dat** and **gaussianwhitenoise_002.dat** will be assigned the keywords ‘noise’ and ‘gaussian’, and the time series stored in the file **sinusoid_001.dat** will be added with keywords ‘periodic’ and ‘sine’.
Note that keywords should be separated only by commas and not whitespace.

When keywords are provided, time series are indexed according to them in the **TimeSeriesKeywords** table and associated index table, **TsKeywordsRelate** of the database.
Assigning keywords to time series makes it easier to retrieve a set of time series with a given set of keywords for analysis, and to group time series annotated with different keywords for classification tasks.

### Time-series data files

`SQL_add` will attempt to find each time-series data file specified in the input file and read it (using `dlmread`).
Data files should thus be accessible in the Matlab path.
Each time-series data file should have a single real number on each row, specifying the ordered values that make up the time series.
Once imported, the time-series data is stored in the database; thus the original time-series data files are no longer required, and can be removed from the Matlab path.