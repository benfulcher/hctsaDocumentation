# Interpreting features

A typical data analysis procedure might first involve carefully inspecting your data, then thinking about the patterns you might want to capture, then trying to devise an algorithm to capture them (testing the algorithm carefully on simulated data, perhaps, to test its behavior, dependence on noise level, time-series length, sensitivity to low-order trends or other types of stationarity, etc.). But in _hctsa_ we do something somewhat dangerous—apply thousands of methods without even needing to first even look at the data :astonished:

![](<../.gitbook/assets/image (1) (1).png>)

A conventional data analysis pipeline starts with the hard work of thinking about the dynamics and carefully selecting time-series analysis methods with deep knowledge of how they work and the assumptions that make their interpretation valid. This hard work is not avoided in _hctsa_, although if you're lucky, _hctsa_ may save you a bunch of work—if it identifies high-performing features, you can focus your energy on interpreting just these. But this hard work comes at the end of an analysis, and is difficult and time-consuming to do well—often involving looking deeply into the algorithms and theory underlying these methods, combined with careful inspection of the data to ensure you are properly interpreting them.

Being the most substantial challenge—one that cannot be automated and requires alot of careful thought—this page provides some basic advice. While it's often challenging to work out what a given feature is capturing in your dataset, it is the most interesting part of an analysis, as it points you to interpretable scientific theory/algorithms, and makes you think carefully about the structure in your time series.

## Finding features to interpret

### 1: Identify significant features&#x20;

Sometimes you will run `TS_TopFeatures` and find no features that significantly distinguish time series recorded from the labeled groups in your dataset. In this case, there may not be a signal, the signal may be too small given your sample size (and the perhaps large number of candidate time-series features, if using the _hctsa_ set), or the right statistical estimators may not be present in _hctsa_.

But other times you will obtain a long list of statistically significant (after multiple hypothesis correction) features (e.g., from `TS_TopFeatures`) with values that significantly distinguish the groups you care about (individuals with some disease diagnosis compared to that of healthy controls). In cases like this, the next step is to obtain some understanding of what's happening.

Consider a long and kinda gnarly list, that is quite hard to make sense of. This is a typical situation because the features names in _hctsa_ are long and typically hard to interpret directly. Like perhaps:

```
[3016] FC_LocalSimple_mean3_taures (forecasting) -- 59.97%
[3067] FC_LocalSimple_median3_taures (forecasting) -- 58.14%
[2748] EN_mse_1-10_2_015_sampen_s3 (entropy,sampen,mse) -- 54.10%
[7338] MF_armax_2_2_05_1_AR_1 (model) -- 53.71%
[7339] MF_armax_2_2_05_1_AR_2 (model) -- 53.31%
[3185] DN_CompareKSFit_uni_psx (distribution,ksdensity,raw,locdep) -- 52.14%
[6912] MF_steps_ahead_ar_best_6_ac1_3 (model,prediction,arfit) -- 52.11%
[6564] WL_coeffs_db3_4_med_coeff (wavelet) -- 52.01%
[4552] SP_Summaries_fft_logdev_fpoly2csS_p1 (spectral) -- 51.57%
[6634] WL_dwtcoeff_sym2_5_noisestd_l5 (wavelet,dwt) -- 51.48%
[930] DN_FitKernelSmoothraw_entropy (distribution,ksdensity,entropy,raw,spreaddep) -- 51.37%
[6574] WL_coeffs_db3_5_med_coeff (wavelet) -- 51.26%
[6630] WL_dwtcoeff_sym2_5_noisestd_l4 (wavelet,dwt) -- 51.04%
[1891] CO_StickAngles_y_ac2_all (correlation) -- 50.85%
[16] rms (distribution,location,raw,locdep,spreaddep) -- 50.83%
[6965] MF_steps_ahead_arma_3_1_6_rmserr_6 (model,prediction) -- 50.83%
[2747] EN_mse_1-10_2_015_sampen_s2 (entropy,sampen,mse) -- 50.35%
[4201] SC_FluctAnal_mag_2_dfa_50_2_logi_ssr (scaling) -- 50.34%
[6946] MF_steps_ahead_ss_best_6_meandiffrms (model,prediction) -- 50.33%
```

If there are hundreds or thousands of statistically significant features, some of which have much higher accuracy than others, you may first want to inspect only a subset of features, e.g., the 50 or 100 top-performing features (with the most significant differences). If you can make sense of these first, you will usually have a pretty good basis for working out what's going on with the features with lower performance (if indeed you want to go beyond interpreting the best-performing features).

### 2: Find groups of similar features

`TS_TopFeatures` is helpful for showing us how features with different, long, and complicated names might cluster into groups of similarly behaving features that are measuring similar properties of the data (cf. the [previous section](ts\_topfeatures.md)). This is essential to interpret groups of features that are capturing the same underlying conceptual property of the dynamics. It helps you work out what's going on, and avoids over-interpreting any specific feature in isolation. For example, sometimes features with very complicated sounding names (like nonlinear embedding dimension estimates) exhibit very similar behavior to very simple features (like lag-1 autocorrelation or distributional outlier measures), indicating that the nonlinear embedding dimension estimates are likely sensitive to simpler properties in the time series (e.g., sensitive to outliers in the data). Intepreting the nonlinear embedding dimension in isolation would be a mistake in such a case.

Plotting and inspecting clustered feature–feature correlation plots are crucial for identifying groups of features with similar behavior on the dataset. Then we can inspect and interpret these groups of similar (highly inter-correlated) features together as a group. This should be the first step in interpreting a set of significant features: which groups of features behave similarly, and what common concepts are they measuring?

An example is here, were [Bailey et al. (2023)](https://www.biorxiv.org/content/10.1101/2023.06.23.546355v1) identified 3 important groups of features—measuring stationarity of self-correlation properties, stationarity of distributional properties, and global distributional properties—and then go on to interpret each in turn:

![](<../.gitbook/assets/image (6).png>)

## Interpreting individual features

When such a group of high-performing features capturing a common time-series property has been identified, how can we start to interpret and understand what each individual feature is measuring?

Some features in the group may be easy to interpret directly. For example, in the list above `rms` is straightforward to interpret directly: it is simply the root-mean-square of the distribution of time-series values. Others have clues in the name (e.g., features starting with `WL_coeffs` are to do with measuring wavelet coefficients, features starting with `EN_mse` correspond to measuring the multiscale entropy ('mse'), and features starting with `FC_LocalSimple_mean` are related to time-series forecasting using local means of the time series).

Below we outline a procedure for how a user can go from a time-series feature selected by _hctsa_ towards a deeper understanding of the type of algorithm that feature is derived from, how that algorithm performs across the dataset, and thus how it can provide interpretable information about your specific time-series dataset.

### Inspecting keywords

The simplest way of getting a quick idea of what sort of property a feature might be measuring is from its keywords, that often label individual features by the class of time-series analysis method from which they were derived. Keywords have not been applied exhaustively to features (this is an ongoing challenge), but when they are there they can be useful at giving a sense of what's going on. In the list above, we see keywords listed in parentheses, such as:

1. _'forecasting'_ (methods related to predicting future values of a time series),
2. _'entropy'_ (methods related to predictability and information content in a time series),
3. _'wavelet'_ (features derived from wavelet transforms of the time series),
4. _'locationDependent'_ (location dependent: features that change under mean shifts of a time series),
5. _'spreadDependent'_ (spread dependent: features that change under rescaling about their mean), and
6. _'lengthDependent'_ (length dependent: features that are highly sensitive to the length of a time series).

### Inspecting code

To find more specific detailed information about a feature, beyond just a broad categorical label of the literature from which it was derived, the next step is find and inspect the code file that generates the feature of interest. For example, say we were interested in the top performing feature in the list above:

```
    [3016] FC_LocalSimple_mean3_taures (forecasting) -- 59.97%
```

We know from the keyword that this feature has something to do with forecasting, and the name provides clues about the details (e.g., `FC_` stands for forecasting, the function `FC_LocalSimple` is the one that produces this feature, which, as the name suggests, performs simple local time-series prediction). We can use the feature ID (`3016`) provided in square brackets to get information from the `Operations` metadata table:

```
>> Operations(Operations.ID==3016,:)
ID                 Name                   Keywords                CodeString              MasterID
____    _____________________________    _____________    _____________________________    ________

3016    'FC_LocalSimple_mean3_taures'    'forecasting'    'FC_LocalSimple_mean3.taures'    836
```

Inspecting the text before the dot, `.`, in the `CodeString` field (`FC_LocalSimple_mean3`) tells us the name that _hctsa_ uses to describe the Matlab function and its unique set of inputs that produces this feature. Whereas the text following the dot, `.`, in the `CodeString` field (`taures`), tells us the field of the output structure produced by the Matlab function that was run.

We can use the `MasterID` to get more information about the code that was run using the `MasterOperations` metadata table:

```
>> MasterOperations(MasterOperations.ID==836,:)
ID             Label                         Code            
___    ______________________    ____________________________

836    'FC_LocalSimple_mean3'    'FC_LocalSimple(y,'mean',3)'
```

This tells us that the code used to produce our feature was `FC_LocalSimple(y,'mean',3)`. We can get information about this function in the command line by running a `help` command:

```
>> help FC_LocalSimple
FC_LocalSimple    Simple local time-series forecasting.

Simple predictors using the past trainLength values of the time series to
predict its next value.

---INPUTS:
y, the input time series

forecastMeth, the forecasting method:
         (i) 'mean': local mean prediction using the past trainLength time-series
                      values,
         (ii) 'median': local median prediction using the past trainLength
                        time-series values
         (iii) 'lfit': local linear prediction using the past trainLength
                        time-series values.

trainLength, the number of time-series values to use to forecast the next value

---OUTPUTS: the mean error, stationarity of residuals, Gaussianity of
residuals, and their autocorrelation structure.
```

We can also inspect the code in the function `FC_LocalSimple` directly for more information. Like all code files for computing time-series features, `FC_LocalSimple.m` is located in the Operations directory of the _hctsa_ repository.

Inspecting the code file, we see that running `FC_LocalSimple(y,'mean',3)` does forecasting using local estimates of the time-series mean (since the second input to `FC_LocalSimple`, `forecastMeth` is set to `'mean'`), using the previous three time-series values to make the prediction (since the third input to `FC_LocalSimple`, `trainLength` is set to `3`).

To understand what the specific output quantity from this code is that came up as being highly informative in our `TS_TopFeatures` analysis, we need to look for the output labeled `taures` of the output structure produced by `FC_LocalSimple`. We discover the following relevant lines of code in `FC_LocalSimple.m`:

```
% Autocorrelation structure of the residuals:
out.ac1 = CO_AutoCorr(res,1,'Fourier');
out.ac2 = CO_AutoCorr(res,2,'Fourier');
out.taures = CO_FirstZero(res,'ac');
```

This shows us that, after doing the local mean prediction, `FC_LocalSimple` then outputs some features on whether there is any residual autocorrelation structure in the residuals of the rolling predictions (the outputs labeled `ac1`, `ac2`, and our output of interest: `taures`).

The code shows that this `taures` output computes the `CO_FirstZero` of the residuals, which measures the first zero of the autocorrelation function (e.g., cf `help CO_FirstZero`). When the local mean prediction still leaves a lot of autocorrelation structure in the residuals, our feature, `FC_LocalSimple_mean3_taures`, will thus take a high value.

### Visualizing outputs

Once we've seen the code that was used to produce a feature, and started to think about how such an algorithm might be measuring useful structure in our time series, we can then check our intuition by inspecting its performance on our dataset (as described in [Investigating specific operations](feature\_summary.md)).

For example, we can run the following:

```
TS_FeatureSummary(3016,'raw',true);
```

which produces a plot like that shown below. We have run this on a dataset containing noisy sine waves, labeled 'noisy' (red) and periodic signals without noise, labeled 'periodic' (blue):

![](../.gitbook/assets/FeatureSummaryForInterpretation.png)

In this plot, we see how this feature orders time series (with the distribution of values shown on the left, and split between the two groups: 'noisy', and 'periodic'). Our intuition from the code, that time series with longer correlation timescales will have highly autocorrelated residuals after a local mean prediction, appears to hold visually on this dataset.

In general, the mechanism provided by `TS_FeatureSummary` to visualize how a given feature orders time series, including across labeled groups, can be very useful for feature interpretation.

## Summary

_hctsa_ contains a large number of features, many of which can be expected to be highly inter-correlated on a given time-series dataset. It is thus crucial to explore how a given feature relates to other features in the library, e.g., using the correlation matrix produced by `TS_TopFeatures` (cf. [Finding informative features](ts\_topfeatures.md)), or by searching for features with similar behavior on the dataset to a given feature of interest (cf. [Finding nearest neighbors](sim\_search.md)).

In a specific domain context, you may need to decide on the trade-off between more complicated features that may have slightly higher in-sample performance on a given task, and simpler, more interpretable features that may help guide domain understanding. The procedures outlined above are typically the first step to understanding a time-series analysis algorithm, and its relationship to alternatives that have been developed across science.
