---
description: >-
  A full list of Matlab code files, organized loosely into broad categories,
  with brief descriptions
---

# List of included code files

## Introduction

The full default set of 7469 features in _hctsa_ is produced by running all of the code files below, many of which produce a large number of outputs (e.g., some functions fit a time-series model and then output statistics including the parameters of the best-fitting model, measures of the model's goodness of fit, the optimal model order, and autocorrelation statistics on the residuals).

In our default feature set, each function is run with multiple input parameters, with each parameter set yielding characteristic outputs. For example,

* `CO_AutoCorr` determine the method in which autocorrelation is computed, as well as the time lag at which autocorrelation is calculated, e.g., lag 1, lag 2, lag 3, etc.
* `WL_DWTCoeff` has inputs that set the mother wavelet to use and level of wavelet decomposition; and
* `FC_LocalSimple` has inputs that determine the time-series forecasting method to use and the size of the training window.

The set of code files below and their input parameters that define the default _hctsa_ feature set are in the `INP_mops_hctsa.txt` file (in the `FeatureSets` directory) of the _hctsa_ repository.

_This page is generated from the current set of `Operations/*.m` files in the repository; categorization into sections below is approximate._

## Distribution

Algorithms for summarizing properties of the distribution of values in a time series (independent of their ordered sequence through time).

| Code file               | Description                                                              |
| ------------------------ | ------------------------------------------------------------------------- |
| `DN_Burstiness`          | Burstiness statistic of a time series.                                    |
| `DN_CompareKSFit`        | Fits a distribution to data.                                              |
| `DN_Cumulants`           | Distributional moments of the input data.                                 |
| `DN_CustomSkewness`      | Custom skewness measures.                                                 |
| `DN_FitKernelSmooth`     | Statistics of a kernel-smoothed distribution of the data.                 |
| `DN_Fit_mle`             | Maximum likelihood distribution fit to data.                              |
| `DN_HistogramAsymmetry`  | Measures of distributional asymmetry.                                     |
| `DN_HistogramMode`       | Mode of a data vector.                                                    |
| `DN_Mean`                | A given measure of location of a data vector.                             |
| `DN_MinMax`              | The maximum and minimum values of the input data vector.                  |
| `DN_Moments`             | A moment of the distribution of the input time series.                    |
| `DN_OutlierInclude`      | How statistics depend on distributional outliers.                         |
| `DN_OutlierTest`         | How distributional statistics depend on distributional outliers.         |
| `DN_PLeft`               | Distance from the mean at which a given proportion of data are more distant. |
| `DN_ProportionValues`    | Proportion of values in a data vector.                                    |
| `DN_Quantile`            | Quantile of the data vector.                                              |
| `DN_RemovePoints`        | How time-series properties change as points are removed.                  |
| `DN_SimpleFit`           | Fits of parametric distributions or simple time-series models.            |
| `DN_Spread`              | Measure of spread of the input time series.                               |
| `DN_TrimmedMean`         | Mean of the outlier-trimmed time series.                                  |
| `DN_Unique`              | The proportion of the time series that are unique values.                 |
| `DN_WithinP`             | Proportion of data points within _p_ standard deviations of the mean.     |
| `DN_CV`                  | Coefficient of variation.                                                 |
| `EN_DistributionEntropy` | Distributional entropy.                                                   |
| `HT_DistributionTest`    | Hypothesis test for distributional fits to a data vector.                 |

## Correlation

Code summarizing basic properties of how values of a time series are correlated through time.

| Code file                | Description                                                              |
| ------------------------- | -------------------------------------------------------------------------- |
| `CO_AddNoise`             | Changes in the automutual information with the addition of noise.         |
| `CO_AutoCorr`             | Compute the autocorrelation of an input time series.                      |
| `CO_AutoCorrShape`        | How the autocorrelation function changes with the time lag.               |
| `CO_AutoCorrX2`           | Asymmetric squared cross-correlation of a time series.                    |
| `CO_AutoCorrX2Shape`      | Shape of the time-reversibility profile of a time series.                 |
| `CO_Embed2`               | Statistics of the time series in a 2-dimensional embedding space.         |
| `CO_Embed2_AngleTau`      | Angle autocorrelation in a 2-dimensional embedding space.                 |
| `CO_Embed2_Basic`         | Point density statistics in a 2-d embedding space.                        |
| `CO_Embed2_Dist`          | Analyzes distances in a 2-d embedding space of a time series.             |
| `CO_Embed2_Shapes`        | Shape-based statistics in a 2-d embedding space.                          |
| `CO_FirstCrossing`        | The first crossing of a given autocorrelation across a given threshold.   |
| `CO_FirstMin`             | Time of first minimum in a given self-correlation function.               |
| `CO_FZCGLSCF`             | The first zero-crossing of the generalized self-correlation function.     |
| `CO_GLSCF`                | The generalized linear self-correlation function of a time series.       |
| `CO_JointNonGaussianity`  | Tests for non-Gaussianity of the joint, time-lagged embedding distribution. |
| `CO_NonlinearAutocorr`    | A custom nonlinear autocorrelation of a time series.                      |
| `CO_PartialAutoCorr`      | Compute the partial autocorrelation of an input time series.              |
| `CO_PosNegAsymmetry`      | Asymmetry of local dynamics between positive and negative regimes.        |
| `CO_StickAngles`          | Analysis of line-of-sight angles between time-series data points.         |
| `CO_TC3`                  | Normalized nonlinear autocorrelation function, `tc3`.                     |
| `CO_TimeRevKLD`           | Kullback-Leibler divergence between forward and time-reversed embeddings. |
| `CO_TranslateShape`       | Statistics on datapoints inside geometric shapes across the time series.  |
| `CO_trev`                 | Normalized nonlinear autocorrelation, `trev` function of a time series.   |
| `CR_RAD`                  | The rescaled auto-density, a metric for inferring structure in the data.  |
| _**Automutual information:**_ |                                                                        |
| `CO_CompareMinAMI`        | Variability in first minimum of automutual information.                   |
| `CO_HistogramAMI`         | The automutual information of the distribution using histograms.          |
| `IN_AutoMutualInfo`       | Time-series automutual information.                                       |
| `IN_AutoMutualInfoStats`  | Statistics on automutual information function for a time series.          |
| `IN_Initialize_MI`        | Initializes an Information Dynamics Toolkit object for MI computation.    |
| `IN_MutualInfo`           | Mutual information of two data vectors.                                   |

## Information Theory

Entropy and complexity measures for time series based on information theory.

| Code file    | Description                                                      |
| ------------- | -------------------------------------------------------------------------- |
| `EN_ApEn`     | Approximate Entropy of a time series.                            |
| `EN_CID`      | Simple complexity measure of a time series.                      |
| `EN_LZComplexity` | Lempel-Ziv complexity of a n-bit encoding of a time series.  |
| `EN_MSE`      | Multiscale entropy of a time series.                             |
| `EN_PermEn`   | Permutation Entropy of a time series.                            |
| `EN_Randomize`| How time-series properties change with increasing randomization. |
| `EN_SampEn`   | Sample Entropy of a time series.                                 |
| `EN_Shannon`  | Approximate Shannon entropy of a time series.                    |
| `EN_rpde`     | Recurrence period density entropy (RPDE).                        |
| `EN_wentropy` | Entropy of time series using wavelets.                           |

## Time-series model fitting and forecasting

Fitting time-series models and doing simple forecasting on time series.

| Code file                | Description                                                                |
| -------------------------- | ---------------------------------------------------------------------------- |
| `MF_AR_arcov`            | Fits an AR model of a given order, p.                                      |
| `MF_CompareAR`           | Compares model fits of various orders to a time series.                    |
| `MF_CompareTestSets`     | Robustness of test-set goodness of fit.                                    |
| `MF_ExpSmoothing`        | Exponential smoothing time-series prediction model.                        |
| `MF_FitSubsegments`      | Robustness of model parameters across different segments of a time series. |
| `MF_GARCHcompare`        | Comparison of GARCH time-series models.                                    |
| `MF_GARCHfit`            | GARCH time-series modeling.                                                |
| `MF_GP_FitAcross`        | Gaussian Process time-series modeling for local prediction.                |
| `MF_GP_Hyperparameters`  | Gaussian Process time-series model parameters and goodness of fit.         |
| `MF_GP_LearnHyperp`      | Learns Gaussian Process hyperparameters for a time series.                 |
| `MF_GP_LocalPrediction`  | Gaussian Process time-series model for local prediction.                   |
| `MF_ResidualAnalysis`    | Canonical summary of the residuals from a model fit (shared by several of the model-fitting operations above). |
| `MF_StateSpaceCompOrder` | Change in goodness of fit across different state space models.             |
| `MF_StateSpace_n4sid`    | State space time-series model fitting.                                     |
| `MF_arfit`               | Statistics of a fitted AR model to a time series.                          |
| `MF_armax`               | Statistics on a fitted ARMA model.                                         |
| `MF_hmm_CompareNStates`  | Hidden Markov Model (HMM) fitting to a time series.                        |
| `MF_hmm_Fit`             | Fits a Hidden Markov Model to sequential data.                             |
| `MF_steps_ahead`         | Goodness of model predictions across prediction lengths.                   |
| `FC_LocalSimple`         | Simple local time-series forecasting.                                      |
| `FC_LoopLocalSimple`     | How simple local forecasting depends on window length.                     |
| `FC_Surprise`            | How surprised you would be of the next data point given recent memory.     |
| `PP_ModelFit`            | Investigates whether AR model fit improves with different preprocessings.  |
| `PP_PreProcess`          | Returns a time series preprocessed in one of several ways (used internally by `PP_Compare`/`PP_Iterate`/`PP_ModelFit`). |

## Stationarity and step detection

Quantifying how properties of a time series change over time.

| Code file               | Description                                                                 |
| ------------------------- | ------------------------------------------------------------------------------ |
| `SY_DriftingMean`       | Mean and variance in local time-series subsegments.                         |
| `SY_DynWin`             | How stationarity estimates depend on the number of time-series subsegments. |
| `SY_KPSStest`           | The KPSS stationarity test.                                                 |
| `SY_LocalDistributions` | Compares the distribution in consecutive time-series segments.              |
| `SY_LocalGlobal`        | Compares local statistics to global statistics of a time series.            |
| `SY_PPtest`             | Phillips-Peron unit root test.                                              |
| `SY_RampingWindows`     | Monotonic trend ('ramping') in windowed statistics.                         |
| `SY_RangeEvolve`        | How the time-series range changes across time.                              |
| `SY_SlidingWindow`      | Sliding window measures of stationarity.                                    |
| `SY_SlowFeatureAnalysis`| Slow feature analysis of windowed statistics.                               |
| `SY_SpreadRandomLocal`  | Bootstrap-based stationarity measure.                                       |
| `SY_StatAv`             | Simple mean-stationarity metric, `StatAv`.                                  |
| `SY_StdNthDer`          | Standard deviation of the nth derivative of the time series.                |
| `SY_StdNthDerChange`    | How the output of `SY_StdNthDer` changes with order parameter.              |
| `SY_nstat_z`            | Cross-forecast errors of zeroth-order time-series models.                   |
| `SY_VarRatioTest`       | Variance ratio test for random walk.                                        |
| _**Step detection:**_   |                                                                              |
| `CP_ML_StepDetect`      | Analysis of discrete steps in a time series.                                |
| `CP_l1pwc_SweepLambda`  | Dependence of step detection on regularization parameter.                   |
| `CP_WaveletVarChg`      | Variance change points in a time series.                                    |

## Nonlinear time-series analysis and fractal scaling

Nonlinear time-series analysis methods, including embedding dimensions, chaos/recurrence measures, and fluctuation analysis. (Note: the previous TSTOOL/OpenTSTOOL-based operations, `NL_TSTL_*`, `TSTL_delaytime`, `TSTL_localdensity`, `NL_crptool_fnn`, and `SD_TSTL_surrogates`, have all been removed -- TSTOOL is no longer part of the repository. The nonlinear methods below are implemented via TISEAN, Michael Small's code, or native Matlab.)

| Code file              | Description                                                                            |
| ------------------------ | ------------------------------------------------------------------------------------------ |
| `NL_BoxCorrDim`        | Correlation dimension of a time series.                                                |
| `NL_c1`                | Information dimension.                                                                 |
| `NL_d2`                | `d2` routine from the TISEAN package.                                                  |
| `NL_DelayTime`         | Optimal delay time using the method of Parlitz and Wichard.                            |
| `NL_Dimensions`        | Box counting, information, and correlation dimension of a time series.                 |
| `NL_DVV`               | Delay Vector Variance method for real and complex signals.                             |
| `NL_EmbedKernelPCA`    | Kernel PCA of a time series in an embedding space, vs. linear PCA.                     |
| `NL_EmbedPCA`          | Principal Components analysis of a time series in an embedding space.                  |
| `NL_FNN`               | False nearest neighbors of a time series.                                              |
| `NL_FractalDimensions` | Fractal dimension spectrum, `D(q)`, of a time series.                                  |
| `NL_GPCorrSum`         | Correlation sum scaling by Grassberger-Proccacia algorithm.                            |
| `NL_LargestLyap`       | Largest Lyapunov exponent of a time series.                                            |
| `NL_LocalDensity`      | Local density estimates in the time-delay embedding space.                             |
| `NL_LyapSpec`          | Spectrum of Lyapunov exponents of a time series.                                       |
| `NL_nlpe`              | Normalized drop-one-out constant interpolation nonlinear prediction error.             |
| `NL_NSAMDF`            | Nonlinearity measure derived from the nonlinear average magnitude difference function. |
| `NL_PoincareSection`   | Poincare section analysis of a time series.                                            |
| `NL_ReturnTime`        | Analysis of the histogram of return times.                                             |
| `NL_RQA`               | Recurrence quantification analysis (RQA).                                              |
| `NL_TakensEstimator`   | Taken's estimator for correlation dimension.                                           |
| `NL_ZeroOneTest`       | The 0-1 test for chaos.                                                                |
| `NW_OrdinalPartitionNetwork` | Ordinal partition transition network measures.                                   |
| `SD_MakeSurrogates`    | Generates surrogate time series (used internally by `SD_Surrogates`/`SD_SurrogateTest`/`SP_Bicoherence`). |
| `SD_SurrogateTest`     | Analyzes test statistics obtained from surrogate time series.                          |
| `SD_Surrogates`        | Surrogate time-series analysis.                                                        |
| _**Fluctuation analysis:**_ |                                                                                     |
| `SC_FastDFA`           | Matlab wrapper for Max Little's `ML_fastdfa` code.                                     |
| `SC_FluctAnal`         | Implements fluctuation analysis by a variety of methods.                               |
| `SC_HiguchiFD`         | Higuchi's fractal dimension of a time series.                                          |
| `SC_MMA`               | Physionet implementation of multiscale multifractal analysis.                          |

## Fourier and wavelet transforms, periodicity measures

Properties of the time-series power spectrum, wavelet spectrum, and other periodicity measures.

| Code file            | Description                                                               |
| ---------------------- | ------------------------------------------------------------------------------ |
| `SP_Summaries`       | Statistics of the power spectrum of a time series.                        |
| `SP_SummariesPhase`  | Statistics of the Fourier phase spectrum of a time series.                |
| `SP_Bicoherence`     | Squared bicoherence of a time series.                                     |
| `SP_Cepstrum`        | Cepstral statistics: harmonic (comb) structure of the power spectrum.     |
| `SP_Specparam`       | Separates the power spectrum into aperiodic (1/f) and periodic (oscillatory) components. |
| `SP_SpectralTimeFreq`| Time-varying spectral statistics from a spectrogram.                      |
| `DT_IsSeasonal`      | A simple test of seasonality.                                             |
| `PD_PeriodicityWang` | Periodicity extraction measure of Wang et al. (2007).                     |
| `WL_Coeffs`          | Wavelet decomposition of the time series.                                 |
| `WL_cwt`             | Continuous wavelet transform of a time series.                            |
| `WL_DetailCoeffs`    | Detail coefficients of a wavelet decomposition.                           |
| `WL_DWTCoeff`        | Discrete wavelet transform coefficients.                                  |
| `WL_fBM`             | Parameters of fractional Gaussian noise/Brownian motion in a time series. |
| `WL_modwtvar`        | Multiscale variance decomposition via the maximal overlap DWT.            |
| `WL_scal2frq`        | Frequency components in a periodic time series.                           |
| `WL_wpdBestTree`     | Adaptive best-basis wavelet packet decomposition.                         |

## Symbolic transformations

Properties of a discrete symbolization of a time series.

| Code file                | Description                                                          |
| --------------------------- | -------------------------------------------------------------------------- |
| `SB_BinaryStats`         | Statistics on a binary symbolization of the time series.             |
| `SB_BinaryStats_AR1`     | Binary run-length statistics normalized against an AR(1) null.       |
| `SB_BinaryStretch`       | Characterizes stretches of 0/1 in time-series binarization.          |
| `SB_CoarseGrain`         | Coarse-grains a continuous time series to a discrete alphabet (used internally by several other `SB_`/`FC_Surprise` operations). |
| `SB_MotifThree`          | Motifs in a coarse-graining of a time series to a 3-letter alphabet. |
| `SB_MotifTwo`            | Local motifs in a binary symbolization of the time series.           |
| `SB_TransitionMatrix`    | Transition probabilities between different time-series states.       |
| `SB_TransitionPAlphabet` | How transition probabilities change with alphabet size.              |

## Statistics from biomedical signal processing

Simple time-series properties derived mostly from the heart rate variability (HRV) literature.

| Code file        | Description                                             |
| ------------------ | ------------------------------------------------------------ |
| `MD_hrv_classic` | Classic heart rate variability (HRV) statistics.        |
| `MD_pNN`         | `pNNx` measures of heart rate variability.              |
| `MD_PolVar`      | The `POLVARd` measure of a time series.                 |
| `MD_RawHRVMeas`  | Poincare-plot heart rate variability (HRV) measures of a time series. |

## Basic statistics, trend

Basic statistics of a time series, including measures of trend.

| Code file          | Description                                                               |
| --------------------- | -------------------------------------------------------------------------- |
| `SY_Trend`         | Quantifies various measures of trend in a time series.                    |
| `ST_FitPolynomial` | Goodness of a polynomial fit to a time series.                            |
| `ST_Length`        | Length of an input data vector.                                           |
| `ST_LocalExtrema`  | How local maximums and minimums vary across the time series.              |
| `ST_MomentCorr`    | Correlations between simple statistics in local windows of a time series. |
| `ST_SimpleStats`   | Basic statistics about an input time series.                              |

## Others

Other properties, like extreme values, visibility/network graphs, physics-based simulations, and dependence on pre-processings applied to a time series.

| Code file            | Description                                                                |
| ----------------------- | -------------------------------------------------------------------------- |
| `EX_ExtremeEventOrder` | Temporal patterning of positive vs. negative extreme events.              |
| `EX_MovingThreshold` | Moving threshold model for extreme events in a time series.                |
| `HT_HypothesisTest`  | Statistical hypothesis test applied to a time series.                      |
| `NW_VisibilityGraph` | Visibility graph analysis of a time series.                                |
| `PH_ForcePotential`  | Couples the values of the time series to a dynamical system.               |
| `PH_Walker`          | Simulates a hypothetical walker moving through the time domain.            |
| `PP_Compare`         | Compare how time-series properties change after pre-processing.            |
| `PP_Iterate`         | How time-series properties change in response to iterative pre-processing. |
