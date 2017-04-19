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

### Keywords
The simplest way of interpreting what sort of property a feature might be measuring is from its keywords, that often label individual features by the class of time-series analysis method from which they were derived.
In the list above, we see keywords listed in parentheses, as _'forecasting'_ (methods related to predicting future values of a time series), _'entropy'_ (methods related to predictability and information content in a time series), and _'wavelet'_ (features derived from wavelet transforms of the time series).
There are also keywords like _'locdep'_ (location dependent: features that change under mean shifts of a time series), _'spreaddep'_ (spread dependent: features that change under rescaling about their mean), and _'lengthdep'_ (length dependent: features that are highly sensitive to the length of a time series).

### Inspecting code
Mostly, the user will want to find more specific detailed information beyond just the category of the literature from which a given feature was derived.