# Initiating an *hctsa* analysis using a custom dataset
Formatted input files are used to set up a custom dataset of time-series data, pieces of Matlab code to run (master operations), and associated outputs from that code (operations).
By default, you can simply specify a custom time-series dataset and the default operation library will be used.
In this section we describe how to initiate an *hctsa* analysis, including how to format the input files used in the *hctsa* framework.

## Specifying a set of time series and operations using `TS_Init`

Initiating a dataset for an *hctsa* analysis involves specifying an input file for each of:
1. the time series to analyze (`INP_ts.mat` or `INP_ts.txt`).
2. the code to run (`INP_mops.txt`).
3. the features to extract from that code (`INP_ops.txt`).

Details of how to format these input files are described below.

To use the default library of operations, you can initiate a time-series dataset (e.g., as specified in the .mat file, `INP_test_ts.mat`) using the following:

```matlab
TS_Init('INP_test_ts.mat');
```

To specify all sets of master operations and operations, you can use the following:

```matlab
TS_Init('INP_ts.mat','INP_mops.txt','INP_ops.txt');
```

`TS_Init` produces a Matlab file, `HCTSA.mat`, containing all of the structures required to understand the set of time series, operations, and the results of their computation (explained [here](hctsa_structure.md)).
<!-- Note that if only the first input file is provided, the default *hctsa* library of operations will be used. -->

Through this initialization process, each time series will be assigned a unique ID, as will each master operation, and each operation.

### Using reduced feature sets (e.g., _catch22_)

The full _hctsa_ feature set, specified by `INP_ops.txt` and `INP_mops.txt`, involves significant computation time.
Thus, it can be a good first step to test out your analysis pipeline using a smaller, faster feature set.
An example is the [catch22](https://github.com/chlubba/catch22) set of 22 features.

This feature set is provided within _hctsa_, and it is very fast to compute using compiled C code.
These features are compiled on initial `install` of _hctsa_ (by running `mexAll` from the `Toolboxes/catch22` directory of _hctsa_):

```matlab
TS_Init('INP_ts.mat','INP_mops_catch22.txt','INP_ops_catch22.txt')
```

## Adding time series
When formatting a time series input file, two formats are available:
* *.mat file input*, which is suited to data that are already stored as variables in Matlab,
* *.txt file input*, which is better suited to when each time series is already stored as an individual text file.

Note that when using the .mat file input method, time-series data is stored in the database to six significant figures.
However, when using the .txt file input method, time-series data values are stored in the database as written in the input text file of each time series.

### Input file format 1 (.mat file)

When using a .mat file input, the .mat file should contain three variables:

* `timeSeriesData`: either a *N* x 1 cell (for *N* time series), where each element contains a vector of time-series values, or a *N* x *M* matrix, where each row specifies the values of a time series (all of length *M*).
* `labels`: a *N* x 1 cell of unique strings containing a named label for each time series.
* `keywords`: a *N* x 1 cell of strings, where each element contains a comma-delimited set of keywords (one for each time series), containing *no whitespace*.

An example involving two time series is below.
In this example, we add two time series (showing only the first two values shown of each), which are labeled according to .dat files from a hypothetical EEG experiment, and assigned keywords (which are separated by commas and no whitespace).
In this case, both are assigned keywords 'subject1' and 'eeg' and, additionally, the first time series is assigned 'trial1', and the second 'trial2' (these labels can be used later to retrieve individual time series).
Note that the labels do not need to specify filenames, but can be any useful label for a given time series.

```
timeSeriesData = {[1.45,2.87,...],[8.53,-1.244,...]}; % (a cell of vectors)
labels = {'EEGExperiment_sub1_trail1.dat','EEGExperiment_sub1_trail2.dat'}; % data labels for each time series
keywords = {'subject1,trial1,eeg','subject1,trial2,eeg'}; % comma-delimited keywords for each time series

% Save these variables out to INP_test.mat:
save('INP_test.mat','timeSeriesData','labels','keywords');

% Initialize a new hctsa analysis using these data and the default feature library:
TS_Init('INP_test.mat');
```

### Input file format 2 (text file)

When using a text file input, the input file now specifies filenames of time series data files, which Matlab will then attempt to load (using `dlmread`).
Data files should thus be accessible in the Matlab path.
Each time-series text file should have a single real number on each row, specifying the ordered values that make up the time series.
Once imported, the time-series data is stored in the database; thus the original time-series data files are no longer required, and can be removed from the Matlab path.

The input text file should be formatted as rows with each row specifying two whitespace separated entries: (i) the file name of a time-series data file and (ii) comma-delimited keywords.

For example, consider the following input file, containing three lines (one for each time series to be added to the database):

    gaussianwhitenoise_001.dat     noise,gaussian
    gaussianwhitenoise_002.dat     noise,gaussian
    sinusoid_001.dat               periodic,sine

Using this input file, a new analysis will contain 3 time series, **gaussianwhitenoise_001.dat** and **gaussianwhitenoise_002.dat** will be assigned the keywords ‘noise’ and ‘gaussian’, and the data in **sinusoid_001.dat** will be assigned keywords ‘periodic’ and ‘sine’.
Note that keywords should be separated *only* by commas (and no whitespace).

## Adding master operations
In our system, a *master operation* refers to a piece of Matlab code and a set of input parameters.

Valid outputs from a master operation are:
1. A single real number,
2. A structure containing real numbers,
3. `NaN` to indicate that the input time series is not appropriate for this code.

The (potentially many) outputs from a master operation can thus be mapped to individual operations (or features), which are single real numbers summarizing a time series that make up individual columns of the resulting data matrix.

Two example lines from the input file, `INP_mops.txt` (in the `Database` directory of the repository), are as follows:

    CO_tc3(x_z,1)     CO_tc3_xz_1
    ST_length(x)    ST_length

Each line in the input file specifies two pieces of information, separated by whitespace:
1. A piece of code and its input parameters.
2. A unique label for that master operation (that can be referenced by individual operations).
<!-- ](adding_operations.md) -->

We use the convention that `x` refers to the input time series and `x_z` refers to a *z*-scored transformation of the input time series (i.e., $$(x - \mu_x)/\sigma_x$$).
In the example above, the first line thus adds an entry in the database for running the code `CO_tc3` using a *z*-scored time series as input (`x_z`), with `1` as the second input with the label `CO_tc3_xz_1`, and the second line will add an entry for running the code `ST_length` using the untransformed time series, `x`, with the label `length`.

When the time comes to perform computations on data using the methods in the database, Matlab needs to have path access to each of the master operations functions specified in the database.
For the above example, Matlab will attempt to run both `CO_tc3(x_z,1)` and `ST_length(x)`, and thus the functions `CO_tc3.m` and `ST_length.m` must be in the Matlab path.
Recall that the script `startup.m`, which should be run at the start of each session using *hctsa*, handles the addition of paths required for the default code library.

## Modifying operations (features)

The input file, e.g., `INP_ops.txt` (in the `Database` directory of the repository) should contain a row for every operation, and use labels that correspond to master operations.
An example excerpt from such a file is below:


```
    CO_tc3_xz_1.raw     CO_tc3_1_raw      correlation,nonlinear
    CO_tc3_xz_1.abs     CO_tc3_1_abs      correlation,nonlinear
    CO_tc3_xz_1.num     CO_tc3_1_num      correlation,nonlinear
    CO_tc3_xz_1.absnum  CO_tc3_1_absnum   correlation,nonlinear
    CO_tc3_xz_1.denom   CO_tc3_1_denom    correlation,nonlinear
    ST_length           length            raw,lengthDependent
```

The first column references a corresponding master label and, in the case of master operations that produce structure, the particular field of the structure to reference (after the fullstop), the second column denotes the label for the operation, and the final column is a set of comma-delimited keywords (that must not include whitespace).
Whitespace is used to separate the three entries on each line of the input file.
In this example, the master operation labeled `CO_tc3_xz_1`, outputs is a structure, with fields that are referenced by the first five operations listed here, and the `ST_length` master operation outputs a single number (the length of the time series), which is referenced by the operation named 'length' here.
The two keywords 'correlation' and 'nonlinear' are added to the `CO_tc3_1` operations, while the keywords 'raw' and 'lengthDependent' are added to the operation called `length`.
These keywords can be used to organize and filter the set of operations used for a given analysis task.
