## Interpreting _hctsa_ features

Often an analysis, such as running `TS_TopFeatures`, yields a list of features, such as:

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

Functions like `TS_TopFeatures` are helpful in showing us how these different types of features might cluster into groups that measure similar properties, but even when we have a group of similar features, how can we start to interpret and understand what these features are actually measuring?

### Inspecting keywords

The simplest way of interpreting what sort of property a feature might be measuring is from its keywords, that often label individual features by the class of time-series analysis method from which they were derived.  
In the list above, we see keywords listed in parentheses, as _'forecasting'_ \(methods related to predicting future values of a time series\), _'entropy'_ \(methods related to predictability and information content in a time series\), and _'wavelet'_ \(features derived from wavelet transforms of the time series\).  
There are also keywords like _'locdep'_ \(location dependent: features that change under mean shifts of a time series\), _'spreaddep'_ \(spread dependent: features that change under rescaling about their mean\), and _'lengthdep'_ \(length dependent: features that are highly sensitive to the length of a time series\).

### Inspecting code

Mostly, the user will want to find more specific detailed information beyond just the category of the literature from which a given feature was derived.  
For example, say we were interested in the top performing feature in the list above:  
    \[3016\] FC\_LocalSimple\_mean3\_taures \(forecasting\) -- 59.97%

We may then wish to understand the code file that produces this feature. We can use the feature ID \(3016\) provided in square brackets to get information from the Operations structure array:

```matlab
>> disp(Operations([Operations.ID]==3016));
            ID: 3016
          Name: 'FC_LocalSimple_mean3_taures'
      Keywords: 'forecasting'
    CodeString: 'FC_LocalSimple_mean3.taures'
      MasterID: 836
```

Inspecting the part before the '.' in the CodeString field tells us the name _hctsa_ uses to describe the Matlab function and its unique set of inputs that produces this feature: `FC_LocalSimple_mean3`.  
The text following the dot, '.', tells us the field of the output structure produced by the Matlab function that was run: `taures`.  
We can use the MasterID to get more information about about this from the MasterOperations structure array:

```matlab
>> disp(MasterOperations([MasterOperations.ID]==836));
       ID: 836
    Label: 'FC_LocalSimple_mean3'
     Code: 'FC_LocalSimple(y,'mean',3)'
```

This tells us that the code used to produce our feature was `FC_LocalSimple(y,'mean',3)`.  
We can inspect this code `FC_LocalSimple`, which is, like all code files for computing time-series features, located in the Operations directory of the _hctsa_ repository.  
We can get information about this function in the commandline:

```matlab
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

Inspecting the code used to compute our feature, `FC_LocalSimple(y,'mean',3)`, this tells us that the code is doing forecasting using the mean (since the second input to `FC_LocalSimple`, `forecastMeth` is set to 'mean') of the previous three values (since the third input to `FC_LocalSimple`, `trainLength` is set to 3).  
To understand what the specific output quantity from this code is that came up as being highly informative in our `TS_TopFeatures` analysis, we need to look for the output labeled `taures`.  
For this, we'll need to look into the code file, `FC_LocalSimple`, to see where this output is computed.  
We find the following lines of code within `FC_LocalSimple`:
```matlab
% Autocorrelation structure of the residuals:
out.ac1 = CO_AutoCorr(res,1,'Fourier');
out.ac2 = CO_AutoCorr(res,2,'Fourier');
out.taures = CO_FirstZero(res,'ac');
```
This shows us that, after doing the local mean prediction, this function then outputs some features on whether there is any residual autocorrelation structure in the residuals of the rolling predictions.
We see that the `taures` output computes the `CO_FirstZero` of the residuals, which computes the first zero of the autocorrelation function (e.g., `help CO_FirstZero`).
When the local mean prediction still leaves alot of autocorrelation structure in the residuals, our feature, `FC_LocalSimple_mean3_taures`, will have a high value.

### Looking at outputs
Once we've seen the code that was used to produce a feature, and started to think about how such a computation might be useful for our given time-series analysis problem, we can check out intuition by inspecting its performance on our dataset (as described in [Investigating specific operations](feature_summary.md)).

For example, we can run the following:

```matlab
TS_FeatureSummary(3016,'raw',true);
```