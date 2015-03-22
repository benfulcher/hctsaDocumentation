# Filtering and normalizing the data using `TSQ_normalize`
<!--{#sec:normalization}-->

The first step in analyzing a dataset involves processing the data matrix.
This involves filtering out operations or time series that produced many errors or special-valued outputs, and then normalizing of the output of all operations, which is typically done in-sample, according to an outlier-robust sigmoidal transform (although other normalizing transformations can be selected).
Both of these tasks are performed using the function `TSQ_normalize`.
The `TSQ_normalize` function writes the new, filtered, normalized matrix to a local file called **HCTSA_N**.
This contains normalized, and trimmed versions of the information in `HCTSA_loc.mat`.

## Example Usage
Example usage is as follows:

        TSQ_normalize('scaledSQzscore',[0.8,0.8]);

This filters time series (rows of the data matrix) with more than 20% special values (specifying 0.8), then filters out operations (columns of the data matrix) containing any special values, and then applies the outlier-robust ‘scaledSQzscore’ sigmoidal transformation to all remaining operations (columns).
The filtered, normalized matrix is saved to the file **HCTSA_N.mat**.
Note that the 'scaledSQzscore' transformation does not tolerate distributions with an interquartile range of zero, which will be filtered out.


The first input controls the normalization method, in this case a scaled outlier-robust sigmoidal transformation, and the second input controls the filtering, in this case each time series needs to produce at least 80% good-valued outputs (setting 0.8), or they are removed, and then operations with less than 100% good-valued outputs are removed (setting 1.0).


These can be relaxed, to, for example, [0.7,0.9], which removes time series with less than 70% good values, and then removes operations with less than 90% good values.
When neither value is 1.0, this can leave NaN values in the resulting data matrix, which can affect some calculations that cannot deal with missing values (such as PCA).
Some applications can tolerate some special-valued outputs from operations (like some clustering methods, where distances are simply calculated using those operations that are did not produce special-valued outputs for each pair of objects), but others cannot (like Principal Components Analysis); the filtering parameters should be specified accordingly. Similarly, it makes sense to weight each operation equally for the purposes dimensionality reduction, and thus normalize all operations to the same range using a transformation like ‘scaledSQzscore’, ‘scaledSigmoid’, or ‘mixedSigmoid’.
For the case of calculating mutual information distances between operations, however, one would rather not distort the distributions and perform no normalization, using ‘raw’ or a
linear transformation like ‘zscore’, for example.
The list of implemented normalization transformations can be found in the function `BF_NormalizeMatrix`.

An example usage is as follows:





<!--Another example:-->

<!--        TSQ_normalize('raw',[0.8,1]);-->

<!--This filters time series (rows of the data matrix) with more than 20% special-values, then filters out operations (columns of the data matrix) containing any special values, leaving a data matrix containing no special (or missing) values.-->
<!--No normalizing transformation is applied to the remaining operations.-->

Analysis can now be performed on the data contained in `HCTSA_N.mat`, in the knowledge that different settings for filtering and normalizing the results can be applied at any time by simply rerunning `TSQ_normalize`, which will overwrite the existing `HCTSA_N.mat` with the results of the new normalization and filtration settings.