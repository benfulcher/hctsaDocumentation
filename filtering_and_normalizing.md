# Filtering, and normalizing data using `TS_normalize`
<!--{#sec:normalization}-->

The first step in analyzing a dataset involves processing the data matrix, which can be done using `TS_normalize`.
This involves filtering out operations or time series that produced many errors or special-valued outputs, and then normalizing of the output of all operations, which is typically done in-sample, according to an outlier-robust sigmoidal transform (although other normalizing transformations can be selected).
Both of these tasks are performed using the function `TS_normalize`.
The `TS_normalize` function writes the new, filtered, normalized matrix to a local file called `HCTSA_N.mat`.
This contains normalized, and trimmed versions of the information in `HCTSA.mat`.

Example usage is as follows:
```matlab
    TS_normalize('scaledRobustSigmoid',[0.8,1.0]);
```
The first input controls the normalization method, in this case a scaled, outlier-robust sigmoidal transformation, specified with 'scaledRobustSigmoid'.
The second input controls the filtering of time series and operations based on minimum thresholds for good values in the corresponding rows (corresponding to time series; filtered first) and columns (corresponding to operations; filtered second) of the data matrix.

In the example above, time series (rows of the data matrix) with more than 20% special values (specifying 0.8) are first filtered out, and then operations (columns of the data matrix) containing any special values (specifying 1.0) are removed.
Columns with approximately constant values are also filtered out.
After filtering the data matrix, the outlier-robust ‘scaledRobustSigmoid’ sigmoidal transformation is applied to all remaining operations (columns).
The filtered, normalized matrix is saved to the file `HCTSA_N.mat`.

Details about what normalization is saved to the `HCTSA_N.mat` file as `normalizationInfo`, a structure that contains the normalization function, filtering options used, and the corresponding `TS_normalize` code that can be used to re-run the normalization.

<!--The first input controls the normalization method, in this case a , and the second input controls the filtering, in this case each time series needs to produce at least 80% good-valued outputs (setting 0.8), or they are removed, and then operations with less than 100% good-valued outputs are removed (setting 1.0).-->

### Setting the normalizing transformation

It makes sense to weight each operation equally for the purposes of dimensionality reduction, and thus normalize all operations to the same range using a transformation like ‘scaledRobustSigmoid’, ‘scaledSigmoid’, or ‘mixedSigmoid’.
For the case of calculating mutual information distances between operations, however, one would rather not distort the distributions and perform no normalization, using ‘raw’ or a
linear transformation like ‘zscore’, for example.
The full list of implemented normalization transformations are listed in the function `BF_NormalizeMatrix`.

Note that the 'scaledRobustSigmoid' transformation does not tolerate distributions with an interquartile range of zero, which will be filtered out; 'mixedSigmoid' will treat these distributions in terms of their standard deviation (rather than interquartile range).

### Setting the filtering parameters

Filtering parameters depend on the application.
Some applications can allow the filtering thresholds to be relaxed.
For example, setting `[0.7,0.9]`, removes time series with less than 70% good values, and then removes operations with less than 90% good values.
<!--When neither value is 1.0, this can leave **NaN** values in the resulting data matrix, which can affect some calculations that cannot deal with missing values (such as PCA).-->
Some applications can tolerate some special-valued outputs from operations (like some clustering methods, where distances are simply calculated using those operations that are did not produce special-valued outputs for each pair of objects), but others cannot (like Principal Components Analysis); the filtering parameters should be specified accordingly.

<!--An example usage is as follows:-->
<!--Another example:-->

<!--        TS_normalize('raw',[0.8,1]);-->

<!--This filters time series (rows of the data matrix) with more than 20% special-values, then filters out operations (columns of the data matrix) containing any special values, leaving a data matrix containing no special (or missing) values.-->
<!--No normalizing transformation is applied to the remaining operations.-->


Analysis can be performed on the data contained in `HCTSA_N.mat` in the knowledge that different settings for filtering and normalizing the results can be applied at any time by simply rerunning `TS_normalize`, which will overwrite the existing `HCTSA_N.mat` with the results of the new normalization and filtration settings.

### Filtering features using `TS_FilterData`

It is often useful to check whether the feature-based classification results of a given analysis is driven by 'trivial' types of features that do not depend on the dynamical properties of the data, e.g., features sensitive to time-series length, location (e.g., mean), or spread (e.g., variance).
Because these features are labeled as `'lengthdep'`, `'locdep'`, and `'spreaddep'`, you can easily filter these out to check the robustness of your analysis.

An example:
```matlab
% Get the IDs of length-dependent features from the `HCTSA.mat` file:
[ID_lengthDep,ID_notlengthDep] = TS_getIDs('lengthdep','raw','ops');

% Generate a new file without these features, called 'HCTSA_locFilt':
TS_FilterData('raw',[],ID_notlengthDep,'HCTSA_locFilt.mat');
```

You could use the same template to filter `'locdep'` or `'spreaddep'` features (or any other combination of keyword labels).
You can then go ahead with analyzing the filtered HCTSA dataset as above, except using your new filename, `HCTSA_locFilt`.
