# Assigning group labels to time series using `TS_LabelGroups`
<!--{#sec:grouping_variables}-->

_hctsa_ allows you to map time-series data into a unified feature space, a representation that is well suited to then applying machine learning methods to perform classification where the goal is to predict a class label assigned to each time series from features of its dynamics.

Class labels can be assigned to time series in an _hctsa_ dataset using the function `TS_LabelGroups`, which populates the `Group` column of the `TimeSeries` table as a categorical labeling of the data (the class labels the classifier will attempt to predict).
These group labels are used by a range of analysis functions, including `TS_PlotLowDim`, `TS_TopFeatures`, and `TS_Classify`.

The machinery of `TS_LabelGroups` assumes that the class labels you are interested in are contained in the `Keywords` column of the `TimeSeries` table (set up from your original [input file](input_files.md) when `TS_Init` was run).

## Labeling

### Manual labeling

If you want to label according to a specific set of keywords, you can do this by specifying the keywords that define the unique groups.
The example below assigns labels to two groups of time series in the `HCTSA.mat` (specifying the shorthand `'raw'` for this default, un-normalized data), corresponding to those labeled as `'parkinsons'` and those labeled as `'healthy'`:

```matlab
TS_LabelGroups('raw',{'parkinsons','healthy'});
```

Note that any time series that are not labeling by either `'parkinsons'` nor `'healthy'` are left unlabeled.

### Automatic labeling

If every time series has a single keyword that uniquely labels it, `TS_LabelGroups` can automatically detect this by running `TS_LabelGroups('raw',{});`.

### Complex labeling

More complex labeling (e.g., using custom combinations of keywords) is not implemented in `TS_LabelGroups`, but can be achieved by writing a script that does logical operations on calls to `TS_LabelGroups` and saves back to the `TimeSeries.Group` column.
Here is [an example](https://github.com/benfulcher/hctsa_phenotypingFly/blob/master/labelCombination.m) of combining two labels (male/female and day/night) on fly movement data.


## Working with labels

By default, the group labels are saved back to the data file (in this example, `HCTSA.mat`).

Group labels can be reassigned at any time by re-running the `TS_LabelGroups` function, and can be cleared by running, e.g., `TS_LabelGroups('raw','clear')`.

Assigned labels are used by the analysis, plotting, and classification functions of _hctsa_.

_Note_: If you assign a labeling to a given dataset (e.g., `HCTSA.mat`), then this labeling will remain with the normalized data (e.g., after running `TS_Normalize`).
