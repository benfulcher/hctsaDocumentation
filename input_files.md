# Setting up a custom dataset
Formatted input files are used to set up a custom dataset of time-series data, pieces of Matlab code to run (master operations), and associated outputs from that code (operations).
By default, the set of operations and master operations is set to our library, and all that needs to be specified is a custom time-series dataset.
In this section we describe the format of the input files used in the *hctsa* framework.

## Adding time series
When formatting a time series input file, two formats are available:
* *.mat file input*, which is suited to data that are already stored as variables in Matlab,
* *.txt file input*, which is better suited to when each time series is already stored as an individual text file.

Note that when using the .mat file input method, time-series data is stored in the database to six significant figures.
However, when using the .txt file input method, time-series data values are stored in the database as written in the input text file of each time series.

### Input file format 1 (.mat file)

When using a .mat file input, the `SQL_add` function expects the .mat file to contain three variables:

* `timeSeriesData`: either a *N* x 1 cell (for *N* time series), where each element contains a vector of time-series values, or a *N*x*M* matrix, where each row specifies the values of a time series (all of length *M*).
* `labels`: a *N* x 1 cell of unique strings containing a named label for each time series.
* `keywords`: a *N* x 1 cell of strings, where each element contains a comma-delimited set of keywords (one for each time series).

An example involving two time series is below.
In this example, we add two time series (showing only the first two values shown of each), which are labeled according to .dat files from a hypothetical EEG experiment, and assigned keywords (which are separated by commas and no whitespace).
In this case, both are assigned keywords 'subject1' and 'eeg' and, additionally, the first time series is assigned 'trial1', and the second 'trial2' (these labels can be used later to retrieve individual time series).
Note that the labels do not need to specify filenames, but can be any useful label for a given time series.

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

When using a text file input, the input file now specifies filenames of time series to be added to the database, which Matlab will then attempt to load (using `dlmread`), and then store in the database.
Data files should thus be accessible in the Matlab path.
Each time-series data file should have a single real number on each row, specifying the ordered values that make up the time series.
Once imported, the time-series data is stored in the database; thus the original time-series data files are no longer required, and can be removed from the Matlab path.

The input text file should be formatted as rows with each row specifying two whitespace separated entries: (i) the file name of a time-series data file and (ii) comma-delimited keywords.

For example, consider the following input file, containing three lines (one for each time series to be added to the database):

    gaussianwhitenoise_001.dat     noise,gaussian
    gaussianwhitenoise_002.dat     noise,gaussian
    sinusoid_001.dat               periodic,sine

Running `SQL_add` with this input file will add three time series to the database.
The time series stored in the files **gaussianwhitenoise_001.dat** and **gaussianwhitenoise_002.dat** will be assigned the keywords ‘noise’ and ‘gaussian’, and the time series stored in the file **sinusoid_001.dat** will be added with keywords ‘periodic’ and ‘sine’.
Note that keywords should be separated only by commas and not whitespace.


## Adding master operations
In our system, a *master operation* refers to a piece of Matlab code and a set of input parameters.

Valid outputs from a master operation are:
1. A single real number,
2. A structure containing real numbers,
3. **NaN** to indicate that the input time series is not appropriate for this code.

The (potentially many) outputs from a master operation can thus be mapped to individual operations (or features), which are single real numbers summarizing a time series that make up individual columns of the resulting data matrix.

Two example lines from the input file, **INP_mops.txt**, are as follows:

    CO_tc3(y,1)     CO_tc3_y_1
    ST_length(x)    ST_length

Each line in the input file specifies two pieces of information, separated by whitespace:
1. A piece of code and its input parameters.
2. A unique label for that master operation (that can be referenced by [individual operations](adding_operations.md)).

We use the convention that *x* refers to the input time series and *y* refers to a *z*-scored transformation of the input time series (i.e., $$(x - \mu_x)/\sigma_x$$).
In the example above, the first line thus adds an entry in the database for running the code `CO_tc3` using a *z*-scored time series as input (*y*), with ‘1’ as the second input with the label **CO_tc3_y_1**, and the second line will add an entry for running the code `ST_length` using the non-*z*-scored time-series *x*, with the label **length**.

When the time comes to perform computations on data using the methods in the database, Matlab needs to have path access to each of the master operations functions specified in the database.
For the above example, Matlab will attempt to run both `CO_tc3(y,1)` and `ST_length(x)`, and thus the functions `CO_tc3.m` and `ST_length.m` must be in the Matlab path.
Recall that the script `startup.m`, which should be run at the start of each session using *hctsa*, handles the addition of paths required for the default code library.

## Adding operations (features)

The input file, e.g., `INP_ops.txt` (in the **Database** directory of the repository) should contain a row for every operation, and use labels that correspond to master operations.
An example excerpt from such a file is below:


```bash
    CO_tc3_y_1.raw     CO_tc3_1_raw      correlation,nonlinear
    CO_tc3_y_1.abs     CO_tc3_1_abs      correlation,nonlinear
    CO_tc3_y_1.num     CO_tc3_1_num      correlation,nonlinear
    CO_tc3_y_1.absnum  CO_tc3_1_absnum   correlation,nonlinear
    CO_tc3_y_1.denom   CO_tc3_1_denom    correlation,nonlinear
    ST_length          length            raw,lengthDependent
```

The first column references a corresponding master label and, in the case of master operations that produce structure, the particular field of the structure to reference (after the fullstop), the second column denotes the label for the operation, and the final column is a set of comma-delimited keywords (that must not include whitespace).
Whitespace is used to separate the three entries on each line of the input file.
In this example, the master operation labeled `CO_tc3_y_1`, outputs is a structure, with fields that are referenced by the first five operations listed here, and the `ST_length` master operation outputs a single number (the length of the time series), which is referenced by the operation named **'length'** here.
The two keywords 'correlation' and 'nonlinear' are added to the `CO_tc3_1` operations, while the keywords ‘raw’ and ‘lengthDependent’ are added to the operation called `length`.
These keywords can be used to organize and filter the set of operations used for a given analysis task.
