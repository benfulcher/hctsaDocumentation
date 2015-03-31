# Low dimensional representations

The software also provides a basic means of visualizing low-dimensional representations of the data, using PCA as `TSQ_plot_pca`.

This can be done for a time-series dataset as follows:

    TSQ_plot_pca('norm','ts')
    
This uses the normalized data (specifying `'norm'`), plotting time series (specifying `'ts'`) in the reduced, two-dimensional principal components space of operations (the leading two principal components of the data matrix).

By default, the user will be prompted to select 10 points on the plot to annotate with their corresponding time series, which are annotated as the first 300 points of that time series (and their names by default).

After selecting 10 points, we have the following:

![pca_image](pca_ungrouped.png)


## Customizing annotations

Annotation properties can be altered with some detail by specifying properties as the `annotateParams` input variable, for example:

```matlab
    % First set up the annotateParams structure:
    annotateParams = struct;
    annotateParams.n = 8; % annotate 8 time series
    annotateParams.maxL = 150; % annotates the first 150 samples of time series
    annotateParams.userInput = 0; % points not selected by user but allocated randomly
    annotateParams.textAnnotation = 0; % don't display names of annotated time series
    showDistributions = 0; % don't plot marginal distributions
    
    % Then generate a plot using these settings:
    TSQ_plot_pca('norm','ts',showDistributions,'',annotateParams)
```

yields:

![annotated plot](lowDimAnnotated.png)

## Plotting groups of time series

If groups of time series have been specified (using `TSQ_LabelGroups`)