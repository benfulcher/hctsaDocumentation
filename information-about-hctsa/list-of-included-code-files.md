---
description: >-
  A full list of Matlab code files, organized loosely into broad categories,
  with brief descriptions
---

# List of included code files

## Introduction

The full default set of over 7700 features in _hctsa_ is produced by running all of the code files below, many of which produce a large number of outputs (e.g., some functions fit a time-series model and then output statistics including the parameters of the best-fitting model, measures of the model's goodness of fit, the optimal model order, and autocorrelation statistics on the residuals).

In our default feature set, each function is run with multiple input parameters, with each parameter set yielding characteristic outputs. For example,

* `CO_AutoCorr` determine the method in which autocorrelation is computed, as well as the time lag at which autocorrelation is calculated, e.g., lag 1, lag 2, lag 3, etc.
* `WL_dwtcoeff` has inputs that set the mother wavelet to use and level of wavelet decomposition; and
* `FC_LocalSimple` has inputs that determine the time-series forecasting method to use and the size of the training window.

The set of code files below and their input parameters that define the default _hctsa_ feature set are in the `INP_mops.txt` file of the _hctsa_ repository.

## Distribution

Algorithms for summarizing properties of the distribution of values in a time series (independent of their ordered sequence through time).

| Code file                | Description                                                                  |
| ------------------------ | ---------------------------------------------------------------------------- |
| `DN_Burstiness`          | Burstiness statistic of a time series.                                       |
| `DN_CompareKSFit`        | Fits a distribution to data.                                                 |
| `DN_CustomSkewness`      | Custom skewness measures.                                                    |
| `DN_FitKernelSmooth`     | Statistics of a kernel-smoothed distribution of the data.                    |
| `DN_Fit_mle`             | Maximum likelihood distribution fit to data.                                 |
| `DN_HighLowMu`           | The highlowmu statistic.                                                     |
| `DN_HistogramMode`       | Mode of the histogram.                                                       |
| `DN_Mean`                | A given measure of location of a data vector.                                |
| `DN_MinMax`              | The maximum and minimum values of the input data vector                      |
| `DN_Moments`             | A moment of the distribution of the input time series.                       |
| `DN_OutlierInclude`      | How statistics depend on distributional outliers.                            |
| `DN_OutlierTest`         | How distributional statistics depend on distributional outliers.             |
| `DN_ProportionValues`    | Proportion of values in a time-series vector.                                |
| `DN_Quantile`            | Quantiles of the distribution of values in the time series data vector.      |
| `DN_RemovePoints`        | How time-series properties change as points are removed.                     |
| `DN_SimpleFit`           | Fits of parametric distributions or simple time-series models.               |
| `DN_Spread`              | Spread of the input time series.                                             |
| `DN_TrimmedMean`         | Mean of the outlier-trimmed time series.                                     |
| `DN_HistogramAsymmetry`  | Distributional asymmetry.                                                    |
| `DN_Unique`              | The proportion of the time series that are unique values.                    |
| `DN_Withinp`             | Proportion of data points within _p_ standard deviations of the mean.        |
| `DN_cv`                  | Coefficient of variation.                                                    |
| `DN_pleft`               | Distance from the mean at which a given proportion of data are more distant. |
| `EN_DistributionEntropy` | Distributional entropy.                                                      |
| `HT_DistributionTest`    | Hypothesis test for distributional fits to a data vector.                    |

## Correlation

Code summarizing basic properties of how values of a time series are correlated through time.

| Code file                     | Description                                                              |
| ----------------------------- | ------------------------------------------------------------------------ |
| `CO_AddNoise`                 | Changes in the automutual information with the addition of noise.        |
| `CO_AutoCorr`                 | Compute the autocorrelation of an input time series.                     |
| `CO_AutoCorrShape`            | How the autocorrelation function changes with the time lag.              |
| `CO_Embed2`                   | Statistics of the time series in a 2-dimensional embedding space.        |
| `CO_Embed2_AngleTau`          | Angle autocorrelation in a 2-dimensional embedding space.                |
| `CO_Embed2_Basic`             | Point density statistics in a 2-d embedding space.                       |
| `CO_Embed2_Dist`              | Analyzes distances in a 2-d embedding space of a time series.            |
| `CO_Embed2_Shapes`            | Shape-based statistics in a 2-d embedding space.                         |
| `CO_FirstCrossing`            | First time the autocorrelation function crosses a threshold.             |
| `CO_FirstMin`                 | Time of first minimum in a given correlation function.                   |
| `CO_NonlinearAutocorr`        | A custom nonlinear autocorrelation of a time series.                     |
| `CO_StickAngles`              | Analysis of line-of-sight angles between time-series data points.        |
| `CO_TranslateShape`           | Statistics on datapoints inside geometric shapes across the time series. |
| `CO_f1ecac`                   | The 1/e correlation length.                                              |
| `CO_fzcglscf`                 | The first zero-crossing of the generalized self-correlation function.    |
| `CO_glscf`                    | The generalized linear self-correlation function of a time series.       |
| `CO_tc3`                      | Normalized nonlinear autocorrelation function, `tc3`.                    |
| `CO_trev`                     | Normalized nonlinear autocorrelation, `trev` function of a time series.  |
| `DK_crinkle`                  | Computes James Theiler's crinkle statistic.                              |
| `DK_theilerQ`                 | Computes Theiler's _Q_ statistic.                                        |
| `DK_timerev`                  | Time reversal asymmetry statistic.                                       |
| `NL_embed_PCA`                | Principal Components analysis of a time series in an embedding space.    |
| _**Automutual information:**_ |                                                                          |
| `CO_RM_AMInformation`         | Automutual information (Rudy Moddemeijer implementation).                |
| `CO_CompareMinAMI`            | Variability in first minimum of automutual information.                  |
| `CO_HistogramAMI`             | The automutual information of the distribution using histograms.         |
| `IN_AutoMutualInfoStats`      | Statistics on automutual information function for a time series.         |

## Information Theory

Entropy and complexity measures for time series based on information theory

| Code file            | Description                                                      |
| -------------------- | ---------------------------------------------------------------- |
| `EN_ApEn`            | Approximate Entropy of a time series.                            |
| `EN_CID`             | Simple complexity measure of a time series.                      |
| `EN_MS_LZcomplexity` | Lempel-Ziv complexity of a n-bit encoding of a time series.      |
| `EN_MS_shannon`      | Approximate Shannon entropy of a time series.                    |
| `EN_PermEn`          | Permutation Entropy of a time series.                            |
| `EN_RM_entropy`      | Entropy of a time series using Rudy Moddemeijer's code.          |
| `EN_Randomize`       | How time-series properties change with increasing randomization. |
| `EN_SampEn`          | Sample Entropy of a time series.                                 |
| `EN_mse`             | Multiscale entropy of a time series.                             |
| `EN_rpde`            | Recurrence period density entropy (RPDE).                        |
| `EN_wentropy`        | Entropy of time series using wavelets.                           |

## Time-series model fitting and forecasting

Fitting time-series models and doing simple forecasting on time series.

| Code file                | Description                                                                |
| ------------------------ | -------------------------------------------------------------------------- |
| `MF_ARMA_orders`         | Compares a range of ARMA models fitted to a time series.                   |
| `MF_AR_arcov`            | Fits an AR model of a given order, p.                                      |
| `MF_CompareAR`           | Compares model fits of various orders to a time series.                    |
| `MF_CompareTestSets`     | Robustness of test-set goodness of fit.                                    |
| `MF_ExpSmoothing`        | Exponential smoothing time-series prediction model.                        |
| `MF_FitSubsegments`      | Robustness of model parameters across different segments of a time series. |
| `MF_GARCHcompare`        | Comparison of GARCH time-series models.                                    |
| `MF_GARCHfit`            | GARCH time-series modeling.                                                |
| `MF_GP_FitAcross`        | Gaussian Process time-series modeling for local prediction.                |
| `MF_GP_LocalPrediction`  | Gaussian Process time-series model for local prediction.                   |
| `MF_GP_hyperparameters`  | Gaussian Process time-series model parameters and goodness of fit.         |
| `MF_StateSpaceCompOrder` | Change in goodness of fit across different state space models.             |
| `MF_StateSpace_n4sid`    | State space time-series model fitting.                                     |
| `MF_arfit`               | Statistics of a fitted AR model to a time series.                          |
| `MF_armax`               | Statistics on a fitted ARMA model.                                         |
| `MF_hmm_CompareNStates`  | Hidden Markov Model (HMM) fitting to a time series.                        |
| `MF_hmm_fit`             | Fits a Hidden Markov Model to sequential data.                             |
| `MF_steps_ahead`         | Goodness of model predictions across prediction lengths.                   |
| `FC_LocalSimple`         | Simple local time-series forecasting.                                      |
| `FC_LoopLocalSimple`     | How simple local forecasting depends on window length.                     |
| `FC_Surprise`            | How surprised you would be of the next data point given recent memory.     |
| `PP_ModelFit`            | Investigates whether AR model fit improves with different preprocessings.  |

## Stationarity and step detection

Quantifying how properties of a time series change over time.

| Code file               | Description                                                                 |
| ----------------------- | --------------------------------------------------------------------------- |
| `SY_DriftingMean`       | Mean and variance in local time-series subsegments.                         |
| `SY_DynWin`             | How stationarity estimates depend on the number of time-series subsegments. |
| `SY_KPSStest`           | The KPSS stationarity test.                                                 |
| `SY_LocalDistributions` | Compares the distribution in consecutive time-series segments.              |
| `SY_LocalGlobal`        | Compares local statistics to global statistics of a time series.            |
| `SY_PPtest`             | Phillips-Peron unit root test.                                              |
| `SY_RangeEvolve`        | How the time-series range changes across time.                              |
| `SY_SlidingWindow`      | Sliding window measures of stationarity.                                    |
| `SY_SpreadRandomLocal`  | Bootstrap-based stationarity measure.                                       |
| `SY_StatAv`             | Simple mean-stationarity metric, `StatAv`.                                  |
| `SY_StdNthDer`          | Standard deviation of the nth derivative of the time series.                |
| `SY_StdNthDerChange`    | How the output of `SY_StdNthDer` changes with order parameter.              |
| `SY_TISEAN_nstat_z`     | Cross-forecast errors of zeroth-order time-series models.                   |
| `SY_VarRatioTest`       | Variance ratio test for random walk.                                        |
| _**Step detection:**_   |                                                                             |
| `CP_ML_StepDetect`      | Analysis of discrete steps in a time series.                                |
| `CP_l1pwc_sweep_lambda` | Dependence of step detection on regularization parameter.                   |
| `CP_wavelet_varchg`     | Variance change points in a time series.                                    |

## Nonlinear time-series analysis and fractal scaling

Nonlinear time-series analysis methods, including embedding dimensions and fluctuation analysis.

| Code file                   | Description                                                                            |
| --------------------------- | -------------------------------------------------------------------------------------- |
| `NL_BoxCorrDim`             | Correlation dimension of a time series.                                                |
| `NL_DVV`                    | Delay Vector Variance method for real and complex signals.                             |
| `NL_MS_fnn`                 | False nearest neighbors of a time series.                                              |
| `NL_MS_nlpe`                | Normalized drop-one-out constant interpolation nonlinear prediction error.             |
| `NL_TISEAN_c1`              | Information dimension.                                                                 |
| `NL_TISEAN_d2`              | `d2` routine from the TISEAN package.                                                  |
| `NL_TISEAN_fnn`             | False nearest neighbors of a time series.                                              |
| `NL_TSTL_FractalDimensions` | Fractal dimension spectrum, `D(q)`, of a time series.                                  |
| `NL_TSTL_GPCorrSum`         | Correlation sum scaling by Grassberger-Proccacia algorithm.                            |
| `NL_TSTL_LargestLyap`       | Largest Lyapunov exponent of a time series.                                            |
| `NL_TSTL_PoincareSection`   | Poincare section analysis of a time series.                                            |
| `NL_TSTL_ReturnTime`        | Analysis of the histogram of return times.                                             |
| `NL_TSTL_TakensEstimator`   | Taken's estimator for correlation dimension.                                           |
| `NL_TSTL_acp`               | `acp` function in TSTOOL                                                               |
| `NL_TSTL_dimensions`        | Box counting, information, and correlation dimension of a time series.                 |
| `NL_crptool_fnn`            | Analyzes the false-nearest neighbors statistic.                                        |
| `SD_SurrogateTest`          | Analyzes test statistics obtained from surrogate time series.                          |
| `SD_TSTL_surrogates`        | Surrogate time-series analysis.                                                        |
| `TSTL_delaytime`            | Optimal delay time using the method of Parlitz and Wichard.                            |
| `TSTL_localdensity`         | Local density estimates in the time-delay embedding space.                             |
| `NL_nsamdf`                 | Nonlinearity measure derived from the nonlinear average magnitude difference function. |
| _**Fluctuation analysis:**_ |                                                                                        |
| `SC_MMA`                    | Physionet implementation of multiscale multifractal analysis                           |
| `SC_fastdfa`                | Matlab wrapper for Max Little's `ML_fastdfa` code                                      |
| `SC_FluctAnal`              | Implements fluctuation analysis by a variety of methods.                               |

## Fourier and wavelet transforms, periodicity measures

Properties of the time-series power spectrum, wavelet spectrum, and other periodicity measures.

| Code file            | Description                                                               |
| -------------------- | ------------------------------------------------------------------------- |
| `SP_Summaries`       | Statistics of the power spectrum of a time series.                        |
| `DT_IsSeasonal`      | A simple test of seasonality.                                             |
| `PD_PeriodicityWang` | Periodicity extraction measure of Wang et al.                             |
| `WL_DetailCoeffs`    | Detail coefficients of a wavelet decomposition.                           |
| `WL_coeffs`          | Wavelet decomposition of the time series.                                 |
| `WL_cwt`             | Continuous wavelet transform of a time series.                            |
| `WL_dwtcoeff`        | Discrete wavelet transform coefficients.                                  |
| `WL_fBM`             | Parameters of fractional Gaussian noise/Brownian motion in a time series. |
| `WL_scal2frq`        | Frequency components in a periodic time series.                           |

## Symbolic transformations

Properties of a discrete symbolization of a time series.

| Code file                | Description                                                          |
| ------------------------ | -------------------------------------------------------------------- |
| `SB_BinaryStats`         | Statistics on a binary symbolization of the time series.             |
| `SB_BinaryStretch`       | Characterizes stretches of 0/1 in time-series binarization.          |
| `SB_MotifThree`          | Motifs in a coarse-graining of a time series to a 3-letter alphabet. |
| `SB_MotifTwo`            | Local motifs in a binary symbolization of the time series.           |
| `SB_TransitionMatrix`    | Transition probabilities between different time-series states.       |
| `SB_TransitionpAlphabet` | How transition probabilities change with alphabet size.              |

## Statistics from biomedical signal processing

Simple time-series properties derived mostly from the heart rate variability (HRV) literature.

| Code file        | Description                                             |
| ---------------- | ------------------------------------------------------- |
| `MD_hrv_classic` | Classic heart rate variability (HRV) statistics.        |
| `MD_pNN`         | `pNNx` measures of heart rate variability.              |
| `MD_polvar`      | The `POLVARd` measure of a time series.                 |
| `MD_rawHRVmeas`  | Heart rate variability (HRV) measures of a time series. |

## Basic statistics, trend

Basic statistics of a time series, including measures of trend.

| Code file          | Description                                                               |
| ------------------ | ------------------------------------------------------------------------- |
| `SY_Trend`         | Quantifies various measures of trend in a time series.                    |
| `ST_FitPolynomial` | Goodness of a polynomial fit to a time series.                            |
| `ST_Length`        | Length of an input data vector.                                           |
| `ST_LocalExtrema`  | How local maximums and minimums vary across the time series.              |
| `ST_MomentCorr`    | Correlations between simple statistics in local windows of a time series. |
| `ST_SimpleStats`   | Basic statistics about an input time series.                              |

## Others

Other properties, like extreme values, visibility graphs, physics-based simulations, and dependence on pre-processings applied to a time series.

| Code file            | Description                                                                |
| -------------------- | -------------------------------------------------------------------------- |
| `EX_MovingThreshold` | Moving threshold model for extreme events in a time series.                |
| `HT_HypothesisTest`  | Statistical hypothesis test applied to a time series.                      |
| `NW_VisibilityGraph` | Visibility graph analysis of a time series.                                |
| `PH_ForcePotential`  | Couples the values of the time series to a dynamical system.               |
| `PH_Walker`          | Simulates a hypothetical walker moving through the time domain.            |
| `PP_Compare`         | Compare how time-series properties change after pre-processing.            |
| `PP_Iterate`         | How time-series properties change in response to iterative pre-processing. |
