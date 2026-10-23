# Filtering and normalizing

The first step in analyzing a dataset involves processing the data matrix, which can be done using `TS_Normalize`. This involves filtering out operations or time series that produced many errors or special-valued outputs, and then normalizing of the output of all operations, which is typically done in-sample, according to an outlier-robust sigmoidal transform \(although other normalizing transformations can be selected\). Both of these tasks are performed using the function `TS_Normalize`. The `TS_Normalize` function writes the new, filtered, normalized matrix to a local file called `HCTSA_N.mat`. This contains normalized, and trimmed versions of the information in `HCTSA.mat`.

Example usage is as follows:

```text
TS_Normalize('mixedSigmoid',[0.8,1.0]);
```

The first input controls the normalization method, in this case a scaled, outlier-robust sigmoidal transformation, specified with `'mixedSigmoid'`. The second input controls the filtering of time series and operations based on minimum thresholds for good values in the corresponding rows \(corresponding to time series; filtered first\) and columns \(corresponding to operations; filtered second\) of the data matrix.

In the example above, time series \(rows of the data matrix\) with more than 20% special values \(specifying 0.8\) are first filtered out, and then operations \(columns of the data matrix\) containing any special values \(specifying 1.0\) are removed.
Columns with approximately constant values are also filtered out.
After filtering the data matrix, the 'mixedSigmoid' transformation is applied to all remaining operations \(columns\): a scaled, outlier-robust sigmoid \('scaledRobustSigmoid'\), falling back to a scaled standard sigmoid \('scaledSigmoid'\) for features with an interquartile range of zero.
The filtered, normalized matrix is saved to the file `HCTSA_N.mat`.
Running `TS_Normalize` with no inputs uses the defaults, `'mixedSigmoid'` and `[0.7,1]`.

Details about the normalization are saved to the `HCTSA_N.mat` file as `normalizationInfo`, a structure that contains the normalization function, filtering options used, and the corresponding `TS_Normalize` code that can be used to re-run the normalization.
It also stores `featureParams` \(a table of the parameters of each kept feature's normalizing transformation, which `TS_Predict` uses to normalize new data in the same way\), `trainIDs` \(the time series the filter and normalization were fitted on\), and `transform` \(the fitted transformation\).

### Fitting the normalization on a training set

By default, the feature filter and normalization are fitted on all time series \(in-sample\).
To keep a set of time series unseen \(e.g., as a test set\), use the `'trainIDs'` option to fit the filter and normalization only on the time series with those IDs; the fitted transformation is then applied unchanged to all time series:

```text
TS_Normalize('mixedSigmoid',[0.7,1],'HCTSA.mat','trainIDs',trainIDs);
```

## Setting the normalizing transformation

It makes sense to weight each operation equally for the purposes of dimensionality reduction, and thus normalize all operations to the same range using a transformation like ‘scaledRobustSigmoid’, ‘scaledSigmoid’, or ‘mixedSigmoid’.
For the case of calculating mutual information distances between operations, however, one would rather not distort the distributions and perform no normalization, using `'none'`, or a linear transformation like ‘zscore’ or ‘maxmin’, for example.
The full list of implemented normalization transformations is given in the function `BF_NormalizeMatrix`: 'subtractMean', 'maxmin', 'zscore', 'sigmoid', 'scaledSigmoid', 'robustSigmoid', 'scaledRobustSigmoid', 'mixedSigmoid', and 'scaledsigmoid5q'.

Note that the 'scaledRobustSigmoid' transformation does not tolerate distributions with an interquartile range of zero, which will be filtered out; 'mixedSigmoid' will treat these distributions in terms of their standard deviation \(rather than interquartile range\).

## Setting the filtering parameters

Filtering parameters depend on the application. Some applications can allow the filtering thresholds to be relaxed. For example, setting `[0.7,0.9]`, removes time series with less than 70% good values, and then removes operations with less than 90% good values.  Some applications can tolerate some special-valued outputs from operations \(like some clustering methods, where distances are simply calculated using those operations that are did not produce special-valued outputs for each pair of objects\), but others cannot \(like Principal Components Analysis\); the filtering parameters should be specified accordingly.

Analysis can be performed on the data contained in `HCTSA_N.mat` in the knowledge that different settings for filtering and normalizing the results can be applied at any time by simply rerunning `TS_Normalize`, which will overwrite the existing `HCTSA_N.mat` with the results of the new normalization and filtration settings.

## Filtering features using `TS_FilterData`

It is often useful to check whether the feature-based classification results of a given analysis is driven by 'trivial' types of features that do not depend on the dynamical properties of the data, e.g., features sensitive to time-series length, location \(e.g., mean\), or spread \(e.g., variance\). Because these features are labeled as `'lengthDependent'`, `'locationDependent'`, and `'spreadDependent'`, you can easily filter these out to check the robustness of your analysis.

An example:

```text
% Get the IDs of length-dependent features from the `HCTSA.mat` file:
[ID_lengthDep,ID_notlengthDep] = TS_GetIDs('lengthDependent','raw','ops');

% Generate a new file without these features, called 'HCTSA_lengthFilt':
TS_FilterData('raw',[],ID_notlengthDep,'HCTSA_lengthFilt.mat');
```

You could use the same template to filter `'locationDependent'` or `'spreadDependent'` features \(or any other combination of keyword labels\).
In the default feature set, only a few features are labeled `'lengthDependent'`, whereas over 40 are labeled `'locationDependent'` and over 40 `'spreadDependent'`.
You can then go ahead with analyzing the filtered HCTSA dataset as above, except using your new filename, `HCTSA_lengthFilt`.

