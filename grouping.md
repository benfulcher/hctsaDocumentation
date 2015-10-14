# Assigning group labels to time series using `TS_LabelGroups`
<!--{#sec:grouping_variables}-->

Highly comparative analyses often involve classification tasks, in which each observation is assigned a (numeric) class label.
Once data has been retrieved, as described above, class labels can be assigned to each time series in a dataset, and stored in the local **HCTSA_*.mat** files using the function `TS_LabelGroups`.

The example below assigns labels to two groups of time series in the **HCTSA_loc.mat** (specifying `'loc'`), corresponding to those labeled as 'parkinsons' and those labeled as 'healthy':

    TS_LabelGroups({'parkinsons','healthy'},'loc');

The first input is a cell specifying the keyword string to use to match each group.

By default, this function saves the group indices back to the data file (in this example, **HCTSA_loc.mat**), by adding a new field, **Group**, to the **TimeSeries** structure array, which contains the group index of each time series.

Group indices stay with the time series they are assigned to after filtering and normalizing the data (using `TS_normalize`).
Group labels can be reassigned at any time by re-running the `TS_LabelGroups` function.

Group labels are used by a range of analysis functions, including `TS_plot_pca`, `TS_TopFeatures`, `TS_classify`.