# Low dimensional representations

The software also provides a basic means of visualizing low-dimensional representations of the data, using PCA as `TSQ_plot_pca`.

This can be done for a time-series dataset as follows:

    TSQ_plot_pca('norm','ts')
    
By default, the user will be prompted to select 10 points on the plot to annotate with time series.
After selecting 10 points, we have the following:

![pca_image](pca_ungrouped.png)

Annotation properties can be altered with some detail by specifying properties as the `annotateParams` input variable, for example:

```matlab
    annotateParams = struct;
    annotateParams.n = 8; % annotate 8 time series
    annotateParams.maxL = 100; % annotates first 100 samples of time series
    annotateParams.userInput = 0; % points not selected by user but allocated randomly
    annotateParams.textAnnotation = 0; % don't display names of annotated time series
    showDistributions = 0; % don't plot marginal distributions
    TSQ_plot_pca('norm','ts',showDistributions,'',annotateParams)
```