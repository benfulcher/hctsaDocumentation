# Adding time series to the database
<!--{#sec:addingTimeSeries}-->

After setting up a database with a library of time-series features, the next task is to add a dataset of time series to the database.
It is up to personal preference of the user whether to keep all time-series data in a single database, or have a different database for each dataset.

Time series are added using the same function used to add master operations and operations to the database, `SQL_add`, which imports time series data (stored in time-series data files) and associated keyword metadata (assigned to each time series) to the database.
The time-series data files to import, and the keywords to assign to each time series are specified in either: (i) an appropriately formatted matlab (`.mat`) file, or (ii) a structured input text file, as explained below.

Every time series added to the database is assigned a unique integer identifier, **ts\_id**, which can be used to retrieve specific time series from the database.

## *Example*: Adding a set of time series to the database
Adding a set of time series to the database requires an appropriately formatted input file, **INP_ts.txt**, for example, the appropriate code is:

    % Add time series (stored in data files) using an input text file:
    SQL_add('ts','INP_ts.txt');
    
    % Add time series (embedded in a .mat file):
    SQL_add('ts','INP_ts.mat');

We provide an example input file in the **Database** directory as **INP_test_ts.txt**, which can be added to the database, following the syntax above, using `SQL_add('ts','INP_test_ts.txt')`.

### Input file format 1 (.mat file)

When using a .mat file input, the `SQL_add` function expects the .mat file to contain three variables:

* `timeSeriesData`: either a *N*x1 cell (for *N* time series), where each element contains a vector of time-series values, or a *N*x*M* matrix, where each row specifies the values of a time series (all of length *M*).
* `labels`: a *N*x1 cell of unique strings containing a named label for each time series.
* `keywords`: a *N*x1 cell of strings, where each element contains a comma-delimited set of keywords (one for each time series).

An example involving two time series (showing only the first two values shown of each):

```
timeSeriesData = {[1.45,2.87,...],[8.53,-1.244,...]}; % (a cell of vectors)
labels = {'informativeLabel1','informativeLabel2'}; % data labels
keywords = {'subject1,trial1,eeg','subject1,trial2,eeg'}; % comma-delimited keywords

% Save these variables out to INP_test.mat:
save('INP_test.mat','timeSeriesData','labels','keywords');

% Add these time series to the database using SQL_add:
SQL_add('ts','INP_test.mat')
```

### Input file format 2 (text file)

The `SQL_add` function expects the input text file to be formatted with each row specifying: (i) the file name of a time-series data file and (ii) comma-delimited keywords, with white space separating them.
For example, consider the following input file, containing three lines (one for each time series to be added to the database):

    gaussianwhitenoise_001.dat     noise,gaussian
    gaussianwhitenoise_002.dat     noise,gaussian
    sinusoid_001.dat               periodic,sine
    
Running `SQL_add` with this input file will add three time series to the database. The time series stored in the files **gaussianwhitenoise_001.dat** and **gaussianwhitenoise_002.dat** will be assigned the keywords ‘noise’ and ‘gaussian’, and the time series stored in the file **sinusoid_001.dat** will be added with keywords ‘periodic’ and ‘sine’.
Note that keywords should be separated only by commas and not whitespace.

When keywords are provided, time series are indexed according to them in the **TimeSeriesKeywords** table and associated index table, **TsKeywordsRelate** of the database.
Assigning keywords to time series makes it easier to retrieve a set of time series with a given set of keywords for analysis, and to group time series annotated with different keywords for classification tasks.

#### Time-series data files

`SQL_add` will attempt to find each time-series data file specified in the input file and read it (using `dlmread`).
Data files should thus be accessible in the Matlab path.
Each time-series data file should have a single real number on each row, specifying the ordered values that make up the time series.
Once imported, the time-series data is stored in the database; thus the original time-series data files are no longer required, and can be removed from the Matlab path.