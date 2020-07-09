# Assigning group labels to time series using `TS_LabelGroups`
<!--{#sec:grouping_variables}-->

_hctsa_ allows you to map time-series data into a unified feature space, a representation that is well suited to then applying machine learning methods to perform classification where the goal is to predict a class label assigned to each time series from features of its dynamics.

Class labels can be assigned to time series in an _hctsa_ dataset using the function `TS_LabelGroups`, which populates the `Group` column of the `TimeSeries` table as a categorical labeling of the data (the class labels the classifier will attempt to predict).
These group labels are used by a range of analysis functions, including `TS_PlotLowDim`, `TS_TopFeatures`, and `TS_Classify`.

The machinery of `TS_LabelGroups` assumes that the class labels you are interested in are contained in the `Keywords` column of the `TimeSeries` table (set up from your original [input file](input_files.md) when `TS_Init` was run).

The example below assigns labels to two groups of time series in the `HCTSA.mat` (specifying the shorthand `'raw'` for this default, un-normalized data), corresponding to those labeled as `'parkinsons'` and those labeled as `'healthy'`:

```matlab
TS_LabelGroups('raw',{'parkinsons','healthy'});
```

If every time series has a single keyword that uniquely labels it, `TS_LabelGroups` can automatically detect this by running `TS_LabelGroups('raw',{});`.

By default, the group labels are saved back to the data file (in this example, `HCTSA.mat`).

Group labels can be reassigned at any time by re-running the `TS_LabelGroups` function, and can be cleared by running, e.g., `TS_LabelGroups('raw','clear')`.

If you assign a labeling to a given dataset (e.g., `HCTSA.mat`), then this labeling will remain with the normalized data (e.g., after running `TS_Normalize`).
