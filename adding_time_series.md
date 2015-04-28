# Adding time series to the database
<!--{#sec:addingTimeSeries}-->

After setting up a database with a library of time-series features, the next task is to add a dataset of time series to the database.
It is up to personal preference of the user whether to keep all time-series data in a single database, or have a different database for each dataset.

Time series are added using the same function used to add master operations and operations to the database, `SQL_add`, which imports time series data (stored in time-series data files) and associated keyword metadata (assigned to each time series) to the database.
The time-series data files to import, and the keywords to assign to each time series are specified in either: (i) an appropriately formatted matlab (`.mat`) file, or (ii) a structured input text file, as explained below.

Time series can be indexed by assigning keywords to them (which are stored in the **TimeSeriesKeywords** table and associated index table, **TsKeywordsRelate** of the database).
Assigning keywords to time series makes it easier to retrieve a set of time series with a given set of keywords for analysis, and to group time series annotated with different keywords for classification tasks.

When added to the *mySQL* database, every time series added to the database is assigned a unique integer identifier, **ts\_id**, which can be used to retrieve specific time series from the database.

## `SQL_add` syntax
Adding a set of time series to the database requires an appropriately formatted input file, **INP_ts.txt**, for example, the appropriate code is:

    % Add time series (embedded in a .mat file):
    SQL_add('ts','INP_ts.mat');
    
    % Add time series (stored in data files) using an input text file:
    SQL_add('ts','INP_ts.txt');

Using an input .mat file is generally easier when the data is already stored as variables in Matlab, whereas the input text file method is better suited to when individual time-series data already exist as text files.

We provide an example input file in the **Database** directory as **INP_test_ts.txt**, which can be added to the database, following the syntax above, using `SQL_add('ts','INP_test_ts.txt')`, as well as a sample .mat file input as **INP_test_ts.mat**, which can be added as `SQL_add('ts','INP_test_ts.mat')`.

Note that when using the .mat file input method, time-series data is stored in the database to six significant figures.
However, when using the .txt file input method, time-series data values are stored in the database as written in the input text file of each time series.

### Input file format 1 (.mat file)

When using a .mat file input, the `SQL_add` function expects the .mat file to contain three variables:

* `timeSeriesData`: either a *N*x1 cell (for *N* time series), where each element contains a vector of time-series values, or a *N*x*M* matrix, where each row specifies the values of a time series (all of length *M*).
* `labels`: a *N*x1 cell of unique strings containing a named label for each time series.
* `keywords`: a *N*x1 cell of strings, where each element contains a comma-delimited set of keywords (one for each time series).

An example involving two time series is below.
In this example, we add two time series (showing only the first two values shown of each), which are labeled according to .dat files from a hypothetical EEG experiment, and assigned keywords (which are separated by commas and no whitespace).
In this case, both are assigned keywords 'subject1' and 'eeg' and, additionally, the first time series is assigned 'trial1', and the second 'trial2' (these labels can be used later to retrieve individual time series).
Note that the labels can be anything, and that keywords are optional.

```
timeSeriesData = {[1.45,2.87,...],[8.53,-1.244,...]}; % (a cell of vectors)
labels = {'EEGExperiment_sub1_trail1.dat','EEGExperiment_sub1_trail2.dat'}; % data labels
keywords = {'subject1,trial1,eeg','subject1,trial2,eeg'}; % comma-delimited keywords

% Save these variables out to INP_test.mat:
save('INP_test.mat','timeSeriesData','labels','keywords');

% Add these time series to the database using SQL_add:
SQL_add('ts','INP_test.mat');
```

### Input file format 2 (text file)

When using a text file input, the input file now specifies filenames of time series to be added to the database, which Matlab will then load and store the data directly into the database (using `dlmread`).
The input text file should be formatted as rows with each row specifying two whitespace separated entries: (i) the file name of a time-series data file and (ii) comma-delimited keywords.

For example, consider the following input file, containing three lines (one for each time series to be added to the database):

    gaussianwhitenoise_001.dat     noise,gaussian
    gaussianwhitenoise_002.dat     noise,gaussian
    sinusoid_001.dat               periodic,sine
    
Running `SQL_add` with this input file will add three time series to the database.
The time series stored in the files **gaussianwhitenoise_001.dat** and **gaussianwhitenoise_002.dat** will be assigned the keywords ‘noise’ and ‘gaussian’, and the time series stored in the file **sinusoid_001.dat** will be added with keywords ‘periodic’ and ‘sine’.
Note that keywords should be separated only by commas and not whitespace.

`SQL_add` will attempt to find each time-series data file specified in the input file and read it (using `dlmread`).
Data files should thus be accessible in the Matlab path.
Each time-series data file should have a single real number on each row, specifying the ordered values that make up the time series.
Once imported, the time-series data is stored in the database; thus the original time-series data files are no longer required, and can be removed from the Matlab path.