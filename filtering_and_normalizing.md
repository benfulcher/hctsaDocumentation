# Inspecting, filtering, and normalizing the data
<!--{#sec:normalization}-->

The first step in analyzing a dataset involves processing the data matrix, which can be done using `TS_normalize`.
In this section we describe how to do this, and also how to visualize any special-valued outputs in your data (using `TS_InspectQuality`).


## Visualizing special values and errors using `TS_InspectQuality`

When applying thousands of time-series analysis methods to diverse datasets, many operations can give results that are not all real numbers.
Some time series may be inappropriate (such as fitting a positive-only distribution to data that is not positive), or measuring stationarity across 2,000 datapoints in time series that are shorter than 2,000 samples.
Other times, an optimization routine may fail, or some unknown error may be called.

It is not necessary, but if one wishes to visualize these situations, the function `TS_InspectQuality` can be used.

It can be run in three modes:

1. `TS_InspectQuality('full');` Plots the full data matrix (all time series as rows and all operations as columns), and shows where each possible special-valued output can occur (including 'error', 'NaN', 'Inf', '-Inf', 'complex', 'empty', or a 'link error').
2. `TS_InspectQuality('reduced');` As `'full'`, but includes only columns where special values occured.
3. `TS_InspectQuality('master');` Plots which types of special-valued outputs were encountered for each master operation.

## Filtering and normalizng data using `TS_normalize`

The first step in analyzing a dataset involves processing the data matrix.
This involves filtering out operations or time series that produced many errors or special-valued outputs, and then normalizing of the output of all operations, which is typically done in-sample, according to an outlier-robust sigmoidal transform (although other normalizing transformations can be selected).
Both of these tasks are performed using the function `TS_normalize`.
The `TS_normalize` function writes the new, filtered, normalized matrix to a local file called **HCTSA_N**.
This contains normalized, and trimmed versions of the information in `HCTSA_loc.mat`.

Example usage is as follows:

        TS_normalize('scaledSQzscore',[0.8,1.0]);

The first input controls the normalization method, in this case a scaled, outlier-robust sigmoidal transformation, specified with 'scaledSQzscore'.
The second input controls the filtering of time series and operations based on minimum thresholds for good values in the corresponding rows (corresponding to time series; filtered first) and columns (corresponding to operations; filtered second) of the data matrix.

In the example above, time series (rows of the data matrix) with more than 20% special values (specifying 0.8) are first filtered out, and then operations (columns of the data matrix) containing any special values (specifying 1.0) are removed.
Columns with approximately constant values are also filtered out.
After filtering the data matrix, the outlier-robust ‘scaledSQzscore’ sigmoidal transformation is applied to all remaining operations (columns).
The filtered, normalized matrix is saved to the file **HCTSA_N.mat**.

Details about what normalization is saved to the **HCTSA_N.mat** file as `normalizationInfo`, a structure that contains the normalization function, filtering options used, and the corresponding `TS_normalize` code that can be used to re-run the normalization.

<!--The first input controls the normalization method, in this case a , and the second input controls the filtering, in this case each time series needs to produce at least 80% good-valued outputs (setting 0.8), or they are removed, and then operations with less than 100% good-valued outputs are removed (setting 1.0).-->

### Setting the normalizing transformation

It makes sense to weight each operation equally for the purposes dimensionality reduction, and thus normalize all operations to the same range using a transformation like ‘scaledSQzscore’, ‘scaledSigmoid’, or ‘mixedSigmoid’.
For the case of calculating mutual information distances between operations, however, one would rather not distort the distributions and perform no normalization, using ‘raw’ or a
linear transformation like ‘zscore’, for example.
The list of implemented normalization transformations can be found in the function `BF_NormalizeMatrix`.

Note that the 'scaledSQzscore' transformation does not tolerate distributions with an interquartile range of zero, which will be filtered out.

### Setting the filtering parameters

Filtering parameters depend on the application.
Some applications can allow the filtering thresholds can be relaxed.
For example, setting `[0.7,0.9]`, removes time series with less than 70% good values, and then removes operations with less than 90% good values.
<!--When neither value is 1.0, this can leave **NaN** values in the resulting data matrix, which can affect some calculations that cannot deal with missing values (such as PCA).-->
Some applications can tolerate some special-valued outputs from operations (like some clustering methods, where distances are simply calculated using those operations that are did not produce special-valued outputs for each pair of objects), but others cannot (like Principal Components Analysis); the filtering parameters should be specified accordingly.



<!--An example usage is as follows:-->
<!--Another example:-->

<!--        TS_normalize('raw',[0.8,1]);-->

<!--This filters time series (rows of the data matrix) with more than 20% special-values, then filters out operations (columns of the data matrix) containing any special values, leaving a data matrix containing no special (or missing) values.-->
<!--No normalizing transformation is applied to the remaining operations.-->



Analysis can now be performed on the data contained in **HCTSA_N.mat**, in the knowledge that different settings for filtering and normalizing the results can be applied at any time by simply rerunning `TS_normalize`, which will overwrite the existing **HCTSA_N.mat** with the results of the new normalization and filtration settings.