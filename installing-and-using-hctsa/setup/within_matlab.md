# Overview of an hctsa analysis

At its core, _hctsa_ analysis involves computing a library of time-series analysis features \(which we call _operations_\) on a time-series dataset. 

The basic sequence of a Matlab-based _hctsa_ analysis is to:

1. _Initialize_ a `HCTSA.mat` file, which contains all of the information about the set of time series and operations in your analysis, as well as the results of applying all operations to all time series, using [`TS_Init`](../calculating/input_files.md).
2. Compute these operations on your time-series data using [`TS_Compute`](../calculating/). The results are structured in the local `HCTSA.mat` file containing matrices \(that store the results of the computations\) and the tables \(that store information about the time-series data and operations\), as described [here](hctsa_structure.md).
3. After the computation is complete, use [a range of processing, analysis, and plotting functions](../analyzing_visualizing/) to understand and interpret the results.

## _Example 1_: Compute a feature vector for a time series

As a quick check of your operation library, you can compute the full default code library on a time-series data vector \(a column vector of real numbers\) as follows:

```text
x = randn(500,1); % A random time-series
featVector = TS_CalculateFeatureVector(x,false); % compute the default feature vector for x
```

Computing all 6392 features in serial \(the `false` input\) can take a minute or so.

## _Example 2_: Analyze a time-series dataset

Suppose you have have a time-series dataset to analyze. You first generate a formatted `INP_ts.mat` input file containing your time series data and associated name and keyword labels, as described [here](../calculating/input_files.md). You then initialize an _hctsa_ calculation using the default library of features:

```text
TS_Init('INP_ts.mat');
```

This generates a local file, `HCTSA.mat` containing the associated metadata for your time series, as well as information about the full time-series feature library \(`Operations`\) and the set of functions and code to call to evaluate them \(`MasterOperations`\), as described [here](hctsa_structure.md).

Next you want to evaluate the code on all of the time series in your dataset. For this you can simply run:

```text
TS_Compute;
```

As described [here](../calculating/running_computations.md), `TS_Compute` has options for larger datasets.
For example, `TS_Compute('series')` computes time series in parallel \(using the Parallel Computing Toolbox\), which is much faster for datasets with many time series, and the last input, `autosaveEvery`, saves partial progress to `HCTSA.mat` at a set interval \(in seconds\) during long runs.

Having run your calculations, you may then want to label your data using the keywords you provided in the case that you have labeled groups of time series:

```text
TS_LabelGroups;
```

and then normalize and filter the data using the default sigmoidal transformation \(`'mixedSigmoid'`\), which also removes time series with fewer than 70% good values, and then features with any bad values, saving the result to `HCTSA_N.mat`:

```text
TS_Normalize;
```

A range of visualization scripts are then available to analyze the results, such as plotting the reordered data matrix:

```text
TS_Cluster; % compute a reordering of data and features
TS_PlotDataMatrix; % plot the data matrix
```

To inspect a low-dimensional representation of the data:

```text
TS_PlotLowDim;
```

Or to determine which features are best at classifying the labeled groups of time series in your dataset:

```text
TS_TopFeatures;
```

Each of these functions can be run with a range of input settings.

