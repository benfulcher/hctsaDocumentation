# Working with hctsa files

When running _hctsa_ analyses, often you want to take subsets of time series \(to look in more detail at a subset of your data\) or subsets of operations \(to explore the behavior of different feature subsets\), or combine multiple subsets of data together \(e.g., as additional data arrive\).

The _hctsa_ package contains a range of functions for these types of tasks, working directly with _hctsa_ .mat files, and are described below.

## Loading data from an _hctsa_ file using `TS_LoadData`

The main variables of an _hctsa_ file \(described [here](../setup/hctsa\_structure.md)\) can be loaded into the workspace using `TS_LoadData`:

```text
[TS_DataMat,TimeSeries,Operations] = TS_LoadData('raw');
```

The input can be a filename or a shorthand: `'raw'` for `HCTSA.mat` and `'norm'` \(the default\) for `HCTSA_N.mat`.
Entries with a nonzero quality label \(errors and special-valued outputs\) are returned as `NaN` in `TS_DataMat`.

## Retrieving time series \(or operations\) of interest by matching on assigned keywords using `TS_GetIDs`

Many time-series classification problems involve filtering subsets of time series based on keyword matching, where keywords are specified in the [input file](input_files.md) provided when initializing a dataset.

Most filtering functions \(such as those listed in this section\), require you to specify a range of IDs of TimeSeries or Operations in order to specify them.
Recall that each TimeSeries and Operation is assigned a unique ID \(stored as the `ID` field in the corresponding metadata table\).
To quickly get the IDs of time series that match a given keyword, the following function can be used:

```text
TimeSeriesIDs = TS_GetIDs(theKeyword,'HCTSA_N.mat');
```

Or the IDs of operations tagged with the 'entropy' keyword:

```text
OperationIDs = TS_GetIDs('entropy','norm','ops');
```

The full set of inputs is `[IDs,notIDs] = TS_GetIDs(theMatchString,whatData,tsOrOps,nameOrKeywords)`, where `whatData` defaults to `'norm'` \(`HCTSA_N.mat`\) and `tsOrOps` to `'ts'`.
Matching is on whole keywords; set `nameOrKeywords` to `'Name'` to match on names instead.
The second output, `notIDs`, gives the IDs that do not match.

These IDs can then be used in the functions below \(e.g., to clear data, or extract a subset of data\).

Note that to get a quick impression of the unique time-series keywords present in a dataset, use the function `TS_WhatKeywords`, which gives a text summary of the unique keywords in an _hctsa_ dataset.

## Clearing or removing data from an _hctsa_ dataset using `TS_LocalClearRemove`

Sometimes you may want to remove a time series from an _hctsa_ dataset because the data was not properly processed, for example. Or one operation may have produced errors because of a missing toolbox reference, or you may have altered the code for an operation, and want to clear the stored results from previous calculations.

For example, often you want to remove from your operation library operations that are dependent on the location of the data \(e.g., its mean: `'locationDependent'`\), that only operate on positive-only time series \(`'posOnly'`\), or that require the TISEAN package \(`'tisean'`\).

The function `TS_LocalClearRemove` achieves these tasks when working directly with .mat files.

`TS_LocalClearRemove` loads in a an _hctsa_ .mat data file, clears or removes the specified time series or operations, and then writes the result back to the file.

_Example 1_: Clear all computed data from time series with IDs 1:5 from `HCTSA.mat` \(specifying `'raw'`\):

```text
TS_LocalClearRemove('raw','ts',1:5,false);
```

_Example 2_: Remove all operations with the keyword 'tisean' \(that depend on the [TISEAN package](http://www.mpipks-dresden.mpg.de/~tisean/Tisean_3.0.1/index.html)\) from `HCTSA.mat`:

```text
TS_LocalClearRemove('raw','ops',TS_GetIDs('tisean','raw','ops'),true);
```

_Example 3_: Remove all operations that require positive-only data \(the `'posOnly'` keyword\) from `HCTSA.mat`:

```text
TS_LocalClearRemove('raw','ops',TS_GetIDs('posOnly','raw','ops'),true);
```

_Example 4_: Remove all operations that are location dependent \(the `'locationDependent'` keyword\) from `HCTSA.mat`:

```text
TS_LocalClearRemove('raw','ops',TS_GetIDs('locationDependent','raw','ops'),true);
```

See the documentation in the function file for additional details about the inputs to `TS_LocalClearRemove`.

## Extracting a subset from an _hctsa_ dataset using `TS_Subset`

Sometimes it's useful to retrieve a subset of an _hctsa_ dataset, when analyzing just a particular class of time series, for example, or investigating a balanced subset of data for time-series classification, or to compare the behavior of a reduced subset of features. This can be done with `TS_Subset`, which takes in a _hctsa_ dataset and generates the desired subset, which can be saved to a new .mat file.

_Example 1_: Import data from 'HCTSA\_N.mat', then save a new dataset containing only time series with IDs in the range 1--100, and all operations, to 'HCTSA\_N\_subset.mat' \(see documentation for all inputs\).

```text
TS_Subset('norm',1:100,[],1,'HCTSA_N_subset.mat')
```

Note that the subset in this case will have be normalized using the full dataset of all time series, and just this subset \(with IDs up to 100\) are now being analyzed. Depending on the normalization method used, different results would be obtained if the subsetting was performed prior to normalization.

_Example 2_: From `HCTSA.mat` \(`'raw'`\), save a subset of that dataset to 'HCTSA\_healthy.mat' containing only time series tagged with the 'healthy' keyword:

```text
TS_Subset('raw',TS_GetIDs('healthy','raw'),[],1,'HCTSA_healthy.mat')
```

## Combining multiple _hctsa_ datasets using `TS_Combine`

When analyzing a growing dataset, sometimes new data needs to be combined with computations on existing data. Alternatively, when computing a large dataset, sometimes you may wish to compute sections of it separately, and may later want to combine each section into a full dataset.

To combine _hctsa_ data files, you can use the `TS_Combine` function.

_Example_: combine _hctsa_ datasets stored in the files `HCTSA_healthy.mat` and `HCTSA_disease.mat` into a new combined file, `HCTSA_combined.mat`:

```text
TS_Combine('HCTSA_healthy.mat','HCTSA_disease.mat',false,false,'HCTSA_combined.mat')
```

The full set of inputs for combining two files is `TS_Combine(HCTSA_1,HCTSA_2,compare_tsids,merge_features,outputFileName,forceWrite)`.

The third input, `compare_tsids`, controls the behavior of the function in combining time series. By setting this to `true`, `TS_Combine` assumes that the TimeSeries IDs are comparable between the datasets \(e.g., because both were produced by the same `TS_Init` run, just computed/subset separately\), and thus filters out duplicates so that the resulting _hctsa_ dataset contains a unique set of time series. By setting this to `false` \(default\), the output will contain a union of time series present in each of the two _hctsa_ datasets. In the case that duplicate TimeSeries IDs exist in the combination file, a new index will be generated in the combined file \(where IDs assigned to time series are re-assigned as unique integers using `TS_ReIndex`\).

In combining operations, `TS_Combine` checks that the same set of operations \(by name\) has been used in both `hctsa` datasets \(and gives an error if they differ\), since operation IDs are only guaranteed to be meaningfully comparable when both files were generated using `TS_Init` with the same feature set.

The fourth input, `merge_features` \(default `false`\), is for the case where the _same_ time series have been computed using _different_ \(disjoint\) feature sets: setting it to `true` combines them into a dataset containing the union of the features.

If the output file \(`HCTSA.mat` by default\) already exists, the result is saved to a new file with `_combined` appended to its name \(e.g., `HCTSA_combined.mat`\), unless the sixth input, `forceWrite`, is `true`, in which case the existing file is overwritten.

### Combining many files

To combine many files at once \(e.g., subsets of a dataset that were computed separately\), pass a cell array of filenames as the first input.
This combines all of them in a single pass, which is much faster than a sequence of pairwise `TS_Combine` calls.
The remaining inputs then shift to `TS_Combine(fileList,outputFileName,compare_tsids,forceWrite)`; for example:

```text
TS_Combine({'HCTSA_1.mat','HCTSA_2.mat','HCTSA_3.mat'},'HCTSA.mat',true)
```

All files must contain the same set of operations \(`merge_features` is not available in this form\).

## Exporting data to .csv files using `OutputToCSV`

To analyze _hctsa_ results in other environments \(e.g., Python or R\), `OutputToCSV` writes the data in an _hctsa_ file to `.csv` files in the current directory:

```text
OutputToCSV('HCTSA.mat');
```

This writes the feature matrix \(`hctsa_datamatrix.csv`, with `NaN` for errors and special-valued outputs\), information about the time series \(`hctsa_timeseries-info.csv`\), and information about the operations \(`hctsa_features.csv`\).
The full set of inputs is `OutputToCSV(whatData,writeTimeSeriesData,writeMasterFeatures,numSigFigs)`: setting `writeTimeSeriesData` to `true` also writes the time-series data \(`hctsa_timeseries-data.csv`\), and setting `writeMasterFeatures` to `true` also writes information about the master operations \(`hctsa_masterfeatures.csv`\).
Values are written with `numSigFigs` significant digits \(15 by default; 17 reproduces each double-precision value exactly\).
