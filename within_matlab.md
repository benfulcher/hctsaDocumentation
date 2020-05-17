# An example analysis workflow using *hctsa* in Matlab

At its core, *hctsa* analysis involves computing a library of time-series analysis features (which we call *operations*) on a time-series dataset.
<!--## Overview of an analysis-->

The basic sequence of a Matlab-based *hctsa* analysis is to:
1. *Initialize* a `HCTSA.mat` file, which contains all of the information about the set of time series and operations in your analysis, as well as the results of applying all operations to all time series, using [`TS_Init`](input_files.md),

2. These operations can be computed on your time-series data using [`TS_Compute`](calculating.md). The results are structured in the local `HCTSA.mat` file containing matrices (that store the results of the computations) and the tables (that store information about the time-series data and operations), as described [here](hctsa_structure.md).

3. After the computation is complete, [a range of processing, analysis, and plotting functions](analyzing_visualizing.md) are provided to understand and interpret the results.

## *Example 1*: Compute a feature vector for a time series

As a quick check of your operation library, you can compute the full default code library on a time-series data vector (a column vector of real numbers) as follows:
```matlab
x = randn(500,1); % A random time-series
featVector = TS_CalculateFeatureVector(x,false); % compute the default feature vector for x
```
## *Example 2*: Analyze a time-series dataset

Suppose you have have a time-series dataset to analyze.
You first generate a formatted `INP_ts.mat` input file containing your time series data and associated name and keyword labels, as described [here](input_files.md).
You then initialize an *hctsa* calculation using the default library of features:
```matlab
TS_Init('INP_ts.mat');
```
This generates a local file, `HCTSA.mat` containing the associated metadata for your time series, as well as information about the full time-series feature library (`Operations`) and the set of functions and code to call to evaluate them (`MasterOperations`), as described [here](hctsa_structure.md).

Next you want to evaluate the code on all of the time series in your dataset.
For this you can simply run:
```matlab
TS_Compute;
```
As described [here](running_computations), or, for larger datasets, using a script to regularly save back to the local file (cf. `sample_runscript_matlab`).

Having run your calculations, you may then want to label your data using the keywords you provided in the case that you have labeled groups of time series:
```matlab
TS_LabelGroups;
```
and then normalize and filter the data using the default sigmoidal transformation:
```matlab
TS_Normalize;
```
A range of visualization scripts are then available to analyze the results, such as plotting the reordered data matrix:
```matlab
TS_Cluster; % compute a reordering of data and features
TS_PlotDataMatrix; % plot the data matrix
```
To inspect a low-dimensional representation of the data:
```matlab
TS_PlotLowDim;
```
Or to determine which features are best at classifying the labeled groups of time series in your dataset:
```matlab
TS_TopFeatures;
```
Each of these functions can be run with a range of input settings.
