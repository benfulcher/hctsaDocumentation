# Adding time series

After setting up a database with a library of time-series features, the next task is to add a dataset of time series to the database. It is up to personal preference of the user whether to keep all time-series data in a single database, or have a different database for each dataset.

Time series are added using the same function used to add master operations and operations to the database, `SQL_Add`, which imports time series data (stored in time-series data files) and associated keyword metadata (assigned to each time series) to the database. The time-series data files to import, and the keywords to assign to each time series are specified in either: (i) an appropriately formatted matlab (`.mat`) file, or (ii) a structured input text file, as explained below.

Note that, when using the `.mat` file input method, time-series data is stored in the database to six significant figures. However, when using the `.txt` file input method, time-series data values are stored as written in the input text file of each time series.

Time series can be indexed by assigning keywords to them (which are stored in the **TimeSeriesKeywords** table and associated index table, **TsKeywordsRelate** of the database). Assigning keywords to time series makes it easier to retrieve a set of time series with a given set of keywords for analysis, and to group time series annotated with different keywords for classification tasks.

When added to the _mySQL_ database, every time series added to the database is assigned a unique integer identifier, **ts\_id**, which can be used to retrieve specific time series from the database.

## `SQL_Add` syntax

Adding a set of time series to the database requires an appropriately formatted input file, **INP\_ts.txt**, for example, the appropriate code is:

```
% Add time series (embedded in a .mat file):
SQL_Add('ts','INP_ts.mat');

% Add time series (stored in data files) using an input text file:
SQL_Add('ts','INP_ts.txt');
```

We provide an example input file in the **Database** directory as **INP\_test\_ts.txt**, which can be added to the database, following the syntax above, using `SQL_Add('ts','INP_test_ts.txt')`, as well as a sample .mat file input as **INP\_test\_ts.mat**, which can be added as `SQL_Add('ts','INP_test_ts.mat')`.
