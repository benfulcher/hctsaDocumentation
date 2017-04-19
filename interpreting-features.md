## Interpreting hctsa features

Often an analysis, such as running `TS_TopFeatures`, yields a list of features, such as:

```
[3016] FC_LocalSimple_mean3_taures (forecasting) -- 59.97%
[3067] FC_LocalSimple_median3_taures (forecasting) -- 58.14%
[2748] EN_mse_1-10_2_015_sampen_s3 (entropy,sampen,mse) -- 54.10%
[7202] MF_StateSpace_n4sid_3_05_1_k_1 (model) -- 53.87%
[7338] MF_armax_2_2_05_1_AR_1 (model) -- 53.71%
[7339] MF_armax_2_2_05_1_AR_2 (model) -- 53.31%
[6635] MF_arfit_1_8_sbc_A1 (modelfit,arfit) -- 52.99%
[6641] MF_arfit_1_8_sbc_maxA (modelfit,arfit) -- 52.99%
[7159] MF_StateSpace_n4sid_2_05_1_k_1 (model) -- 52.19%
[3185] DN_CompareKSFit_uni_psx (distribution,ksdensity,raw,locdep) -- 52.14%
[6912] MF_steps_ahead_ar_best_6_ac1_3 (model,prediction,arfit) -- 52.11%
[6564] WL_coeffs_db3_4_med_coeff (wavelet) -- 52.01%
[6915] MF_steps_ahead_ar_best_6_ac1_4 (model,prediction,arfit) -- 51.86%
[6489] WL_cwt_sym2_32_medianabsC (wavelet,cwt) -- 51.82%
[6918] MF_steps_ahead_ar_best_6_ac1_5 (model,prediction,arfit) -- 51.79%
[4199] SC_FluctAnal_mag_2_dfa_50_2_logi_se1 (scaling) -- 51.75%
[4200] SC_FluctAnal_mag_2_dfa_50_2_logi_se2 (scaling) -- 51.75%
[3180] DN_CompareKSFit_ev_psx (distribution,ksdensity,raw,locdep) -- 51.69%
[6610] WL_dwtcoeff_db3_5_noisestd_l4 (wavelet,dwt) -- 51.65%
[4552] SP_Summaries_fft_logdev_fpoly2csS_p1 (spectral) -- 51.57%
[6634] WL_dwtcoeff_sym2_5_noisestd_l5 (wavelet,dwt) -- 51.48%
[930] DN_FitKernelSmoothraw_entropy (distribution,ksdensity,entropy,raw,spreaddep) -- 51.37%
[7123] MF_StateSpace_n4sid_1_05_1_k_1 (model) -- 51.37%
[6574] WL_coeffs_db3_5_med_coeff (wavelet) -- 51.26%
[6099] PP_Compare_medianf10_statav6 (preprocessing,raw,stationarity) -- 51.11%
[6630] WL_dwtcoeff_sym2_5_noisestd_l4 (wavelet,dwt) -- 51.04%
[6942] MF_steps_ahead_ss_best_6_rmserr_6 (model,prediction) -- 51.03%
[6614] WL_dwtcoeff_db3_5_noisestd_l5 (wavelet,dwt) -- 51.02%
[6646] MF_arfit_1_8_sbc_rmsA (modelfit,arfit) -- 50.97%
[1891] CO_StickAngles_y_ac2_all (correlation) -- 50.85%
[16] rms (distribution,location,raw,locdep,spreaddep) -- 50.83%
[6965] MF_steps_ahead_arma_3_1_6_rmserr_6 (model,prediction) -- 50.83%
[2677] PH_Walker_momentum_5_res_ac1 (trend) -- 50.79%
[6149] PP_Compare_rav2_kscn_peaksepy (preprocessing,raw) -- 50.75%
[2640] PH_Walker_momentum_2_w_std (trend) -- 50.70%
[6644] MF_arfit_1_8_sbc_stdA (modelfit,arfit) -- 50.50%
[2749] EN_mse_1-10_2_015_sampen_s4 (entropy,sampen,mse) -- 50.36%
[2747] EN_mse_1-10_2_015_sampen_s2 (entropy,sampen,mse) -- 50.35%
[4201] SC_FluctAnal_mag_2_dfa_50_2_logi_ssr (scaling) -- 50.34%
[6946] MF_steps_ahead_ss_best_6_meandiffrms (model,prediction) -- 50.33%
```

With names like these, how can we start to interpret and understand what these features are actually measuring?
