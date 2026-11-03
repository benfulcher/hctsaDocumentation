---
description: >-
  A full list of Matlab code files, organized loosely into broad categories,
  with brief descriptions
---

# List of included code files

## Introduction

The full default set of 6392 features in _hctsa_ (v3.0.0) comes from 927 master operations, each of which runs one of the code files below with a particular set of inputs.
Many of these produce a large number of outputs (e.g., some functions fit a time-series model and then output statistics including the parameters of the best-fitting model, measures of the model's goodness of fit, the optimal model order, and autocorrelation statistics on the residuals).

In our default feature set, each function is run with multiple input parameters, with each parameter set yielding characteristic outputs. For example,

* `CO_AutoCorr` has inputs that set the time lag at which the autocorrelation is calculated (e.g., lag 1, lag 2, lag 3, etc.) and the method used to compute it;
* `WL_DWTCoeff` has inputs that set the mother wavelet to use and level of wavelet decomposition; and
* `FC_LocalSimple` has inputs that determine the time-series forecasting method to use and the size of the training window.

The set of code files below and their input parameters that define the default _hctsa_ feature set are in the `INP_mops_hctsa.txt` file (in the `FeatureSets` directory) of the _hctsa_ repository.

_The tables below cover every function registered in `FeatureSets/INP_mops_hctsa.txt` and every file in `Operations/`; categorization into sections is approximate._
Files marked "(used internally)" are not run directly in the default feature set, but are called by operations that are.
Most code files are in the `Operations` directory; the `DK_*` operations are in `Toolboxes/Danny_Kaplan`.

Operations renamed or removed since v2.0.0 are traced in `FeatureSets/renamed_operations.csv`.
For example, `SC_FastDFA` was removed (the DFA in `SC_FluctAnal` covers it), `SY_DynWin`, `MF_ARMA_orders` and `DN_HighLowMu` were removed, `SB_BinaryStretch` was replaced by `SB_BinaryGapHomogeneity`, and `NL_BoxCorrDim` was renamed `NL_BoxCountEntropyRate`.

## Distribution

Algorithms for summarizing properties of the distribution of values in a time series (independent of their ordered sequence through time).

| Code file               | Description                                                              |
| ------------------------ | ------------------------------------------------------------------------- |
| `DN_Burstiness`          | Burstiness statistic of the values of a time series.                      |
| `DN_CompareKSFit`        | Compares a fitted distribution with the kernel-smoothed distribution of the data. |
| `DN_CustomSkewness`      | Custom skewness measures.                                                 |
| `DN_CV`                  | Coefficient of variation.                                                 |
| `DN_FitKernelSmooth`     | Statistics of a kernel-smoothed distribution of the data.                 |
| `DN_HistogramAsymmetry`  | Measures of the asymmetry of the histogram of the data.                   |
| `DN_HistogramMode`       | Mode of a data vector, estimated from a histogram.                        |
| `DN_Mean`                | A given measure of location of a data vector.                             |
| `DN_MinMax`              | The maximum or minimum value of a data vector.                            |
| `DN_Moments`             | A moment of the distribution of the values of a time series.              |
| `DN_OutlierInclude`      | How the timing and spacing of extreme values change as a threshold rises. |
| `DN_OutlierTest`         | How the mean and spread of the values change when the most extreme values are trimmed. |
| `DN_PLeft`               | Distance from the mean beyond which a given proportion of the data lie.   |
| `DN_ProportionValues`    | Proportion of values in a data vector of a given kind.                    |
| `DN_Quantile`            | Quantile of a data vector.                                                |
| `DN_RemovePoints`        | How the distribution of values changes when a set of points is removed or clipped. |
| `DN_SimpleFit`           | Fits a simple parametric curve (e.g., Gaussian, exponential, power law) to the distribution of values. |
| `DN_Spread`              | Measure of spread of the values of a data vector.                         |
| `DN_TailIndex`           | Tail index of the distribution of values: how heavy its tails are.        |
| `DN_TrimmedMean`         | Mean of a data vector after trimming its extreme values.                  |
| `DN_Unique`              | The proportion of the values of a time series that are distinct.          |
| `DN_WithinP`             | Proportion of data points within a distance of the center of the distribution. |
| `EN_DistributionEntropy` | Entropy of the distribution of values.                                    |
| `HT_DistributionTest`    | Goodness of fit of a distribution fitted to a data vector.                |
| `HT_MarginalTests`       | p-value of a hypothesis test about the distribution of values.            |

## Correlation

Code summarizing basic properties of how values of a time series are correlated through time.

| Code file                | Description                                                              |
| ------------------------- | -------------------------------------------------------------------------- |
| `CO_AddNoise`             | How the automutual information falls as noise is added to the series.    |
| `CO_AutoCorr`             | The autocorrelation of a time series at one or more lags.                 |
| `CO_AutoCorrShape`        | The shape of the autocorrelation function.                                |
| `CO_AutoCorrX2`           | Asymmetric squared cross-correlation of a time series (used internally by `CO_AutoCorrX2Shape`). |
| `CO_AutoCorrX2Shape`      | Shape of the time-irreversibility profile of correlations between a series and its square. |
| `CO_Embed2`               | Statistics of the time series in a 2-dimensional delay embedding.         |
| `CO_Embed2_AngleTau`      | Angle autocorrelation in a 2-dimensional delay embedding, over delays.    |
| `CO_Embed2_Basic`         | Point density statistics in a 2-d embedding space.                        |
| `CO_Embed2_Dist`          | Distances between successive points in a 2-d delay embedding.             |
| `CO_Embed2_Shapes`        | Shape-based statistics in a 2-d embedding space.                          |
| `CO_FallingSticks`        | Physical falling-sticks model of line-of-sight interaction.               |
| `CO_FirstCrossing`        | The first crossing of the autocorrelation function across a threshold.    |
| `CO_FirstMin`             | Time of the first minimum (or maximum) in a self-correlation function.    |
| `CO_FZCGLSCF`             | The first zero-crossing of the generalized self-correlation function.     |
| `CO_GLSCF`                | The generalized linear self-correlation function of a time series.       |
| `CO_JointNonGaussianity`  | Tests for non-Gaussianity of the joint, time-lagged embedding distribution. |
| `CO_MatrixProfile`        | How well each subsequence shape recurs elsewhere in the time series.      |
| `CO_NonlinearAutocorr`    | A custom nonlinear autocorrelation of a time series.                      |
| `CO_Oversampling`         | Detects temporal oversampling relative to a series' own dynamics.         |
| `CO_PartialAutoCorr`      | The partial autocorrelation of a time series.                             |
| `CO_PosNegAsymmetry`      | Asymmetry of local dynamics between positive and negative regimes.        |
| `CO_Quantilogram`         | Serial dependence of the series being below its own quantiles.            |
| `CO_RemovePoints`         | How the autocorrelation changes when a set of points is removed or clipped. |
| `CO_StickAngles`          | Analysis of angles between successive same-sign time-series values.       |
| `CO_TC3`                  | Normalized nonlinear autocorrelation, `tc3`.                              |
| `CO_TimeRevKLD`           | Kullback-Leibler divergence between forward and time-reversed embeddings. |
| `CO_TranslateShape`       | Statistics on datapoints inside geometric shapes across the time series.  |
| `CO_trev`                 | Time-reversal asymmetry at a lag, from the cubed increments of the series. |
| `CR_RAD`                  | The rescaled auto-density, a metric for inferring the distance to criticality. |
| `DK_crinkle`              | Theiler's crinkle statistic.                                              |
| `DK_theilerQ`             | Theiler's _Q_ statistic.                                                  |
| `DK_timerev`              | A time-reversal asymmetry statistic.                                      |
| `HT_IndependenceTests`    | Statistic or p-value of a test of serial independence (the Ljung-Box test needs the Econometrics Toolbox). |
| _**Automutual information:**_ |                                                                        |
| `CO_CompareMinAMI`        | Variability of the first minimum of the automutual information.           |
| `CO_HistogramAMI`         | Automutual information of a time series, from a 2-d histogram.            |
| `IN_AutoMutualInfo`       | Time-series automutual information (used internally, e.g., by `CO_FirstMin`, `IN_AutoMutualInfoStats` and the `'mi'` time delay). |
| `IN_AutoMutualInfoStats`  | Statistics on the automutual information function of a time series.       |
| `IN_Initialize_MI`        | Initializes an Information Dynamics Toolkit object for mutual information computation (used internally). |
| `IN_MultivariateAMI`      | Multivariate automutual information, `I(x_t; x_{t-tau}, x_{t-2tau})`.     |

## Information Theory

Entropy and complexity measures for time series based on information theory.

| Code file    | Description                                                      |
| ------------- | -------------------------------------------------------------------------- |
| `EN_ApEn`     | Approximate entropy of a time series.                            |
| `EN_BubbleEn` | Bubble entropy of a time series.                                 |
| `EN_CID`      | Complexity estimate from the length of the time series' line graph. |
| `EN_DispEn`   | Dispersion entropy of a time series.                             |
| `EN_FuzzyEn`  | Fuzzy entropy of a time series.                                  |
| `EN_LZComplexity` | Lempel-Ziv complexity of an n-symbol encoding of a time series. |
| `EN_MSE`      | Multiscale entropy of a time series.                             |
| `EN_PermEn`   | Permutation entropy of a time series.                            |
| `EN_PermEnComplexity` | Jensen-Shannon statistical complexity of ordinal patterns. |
| `EN_RandomizeOrdinal` | How quickly ordinal-pattern structure is lost as a series is randomized. |
| `EN_SampEn`   | Sample entropy of a time series.                                 |
| `EN_Shannon`  | Approximate Shannon entropy of a time series.                    |
| `EN_rpde`     | Recurrence period density entropy (RPDE).                        |
| `EN_wentropy` | Wavelet entropy of a time series.                                |

## Time-series model fitting and forecasting

Fitting time-series models and doing simple forecasting on time series.

| Code file                | Description                                                                |
| -------------------------- | ---------------------------------------------------------------------------- |
| `MF_AR_arcov`            | An AR model of a given order fitted to the time series.                    |
| `MF_CompareAR`           | How the out-of-sample error of an AR model changes with its order.         |
| `MF_CompareTestSets`     | How well a model fitted to the whole series predicts short stretches of it. |
| `MF_ExpSmoothing`        | Exponential smoothing as a one-step forecaster.                            |
| `MF_FitSubsegments`      | How the fit of a model varies across segments of the time series.          |
| `MF_GARCHcompare`        | How well GARCH models of different orders describe the changing variance of the series. |
| `MF_GARCHfit`            | A GARCH model of the changing variance of the series, and what it leaves in the residuals. |
| `MF_GP_FitAcross`        | How well a Gaussian process through a few evenly spaced points reproduces the series. |
| `MF_GP_Hyperparameters`  | Fitted kernel parameters and goodness of fit of a Gaussian process model.  |
| `MF_GP_LearnHyperp`      | Learns Gaussian process hyperparameters for a time series (used internally by `MF_GP_FitAcross` and `MF_GP_Hyperparameters`). |
| `MF_ResidualAnalysis`    | Canonical summary of the residuals from a model fit (used internally by several of the model-fitting operations above). |
| `MF_StateSpaceCompOrder` | How the fit of a state-space model improves as its order increases.        |
| `MF_StateSpace_n4sid`    | A fitted state-space model, and how well it predicts the later part of the series. |
| `MF_arfit`               | Coefficients, order, residuals and oscillation modes of a best-fitting AR model. |
| `MF_armax`               | Coefficients of a fitted ARMA model, and how well it predicts the later part of the series. |
| `MF_hmm_CompareNStates`  | How the fit of hidden Markov models changes with the number of hidden states. |
| `MF_hmm_Fit`             | A hidden Markov model fitted to the first part of the series, and how well it describes the rest. |
| `MF_steps_ahead`         | Accuracy of multi-step-ahead model predictions, relative to trivial predictors, across horizons. |
| `FC_LocalSimple`         | How well a simple local rule forecasts the next value.                     |
| `FC_LoopLocalSimple`     | How simple local forecast errors change with the number of past values used. |
| `FC_Surprise`            | How surprising each next symbol is, given the recent past.                 |
| `PP_ModelFit`            | How the error of an AR model changes after preprocessing the series.       |
| `PP_PreProcess`          | Returns a time series preprocessed in one of several ways (used internally by `PP_ModelFit` and the GARCH operations). |

## Stationarity and step detection

Quantifying how properties of a time series change over time.

| Code file               | Description                                                                 |
| ------------------------- | ------------------------------------------------------------------------------ |
| `SY_DriftingAutoCorr`   | Drift in a lag-tau autocorrelation, via a cumulative-sum test.              |
| `SY_DriftingMean`       | Mean and variance in local time-series subsegments.                         |
| `SY_DriftingMeanCUSUM`  | Parameter-free CUSUM test for a drifting mean.                              |
| `SY_KPSStest`           | The KPSS stationarity test (needs the Econometrics Toolbox).                |
| `SY_LocalDistributions` | Compares the distribution in consecutive time-series segments.              |
| `SY_LocalGlobal`        | Compares local statistics to global statistics of a time series.            |
| `SY_PPtest`             | Phillips-Perron unit root test (needs the Econometrics Toolbox).            |
| `SY_RampingWindows`     | Monotonic trend ('ramping') in windowed statistics.                         |
| `SY_RangeEvolve`        | How the time-series range changes across time.                              |
| `SY_SlidingWindow`      | How a local statistic changes across windows sliding along the series.      |
| `SY_SlowFeatureAnalysis`| Slow feature analysis of windowed statistics.                               |
| `SY_SpreadRandomLocal`  | How local statistics vary across many overlapping segments of the series.   |
| `SY_StatAv`             | Simple mean-stationarity metric, `StatAv`.                                  |
| `SY_StdNthDer`          | Standard deviation of the nth derivative of the time series (used internally by `SY_StdNthDerChange`). |
| `SY_StdNthDerChange`    | How the output of `SY_StdNthDer` changes with the order of the derivative.  |
| `SY_nstat_z`            | Cross-forecast errors of zeroth-order time-series models.                   |
| `SY_VarRatioTest`       | Variance ratio test for random walk (needs the Econometrics Toolbox).       |
| _**Step detection:**_   |                                                                              |
| `CP_ML_StepDetect`      | Fits a staircase of constant levels to the series and describes its steps.  |
| `CP_l1pwc_SweepLambda`  | How step detection changes as its penalty on step size varies.              |
| `CP_WaveletVarChg`      | Number of variance change points found from a wavelet detail signal.        |

## Nonlinear time-series analysis and fractal scaling

Nonlinear time-series analysis methods, including embedding dimensions, chaos/recurrence measures, and fluctuation analysis.
These are implemented in native Matlab, or using TISEAN, Michael Small's code, or ripser (see [External code and toolboxes](#external-code-and-toolboxes) below).
(The earlier TSTOOL/OpenTSTOOL-based operations, `NL_TSTL_*`, `TSTL_delaytime`, `TSTL_localdensity`, `NL_crptool_fnn`, and `SD_TSTL_surrogates`, have all been removed: TSTOOL is no longer part of the repository.)

| Code file              | Description                                                                            |
| ------------------------ | ------------------------------------------------------------------------------------------ |
| `NL_BoxCountEntropyRate` | How the box-counting (order-2 Rényi) entropy of a delay embedding grows with embedding dimension. |
| `NL_c1`                | Information dimension from fixed-mass scaling curves (TISEAN `c1` and `c2d`).          |
| `NL_d2`                | Correlation dimension, correlation entropy and related scaling statistics (TISEAN `d2`). |
| `NL_DelayTime`         | How quickly points that start with nearly equal values drift apart, over a range of delays. |
| `NL_Dimensions`        | Box-counting and correlation dimension of the delay embedding.                         |
| `NL_DVV`               | How well nearby delay vectors predict their target value, compared with surrogates (delay vector variance). |
| `NL_EmbedCluster`      | Whether the time-delay embedding of the series forms separate clusters of points.      |
| `NL_EmbedKernelPCA`    | Kernel PCA of a time-delay embedding of the series, compared with linear PCA.          |
| `NL_EmbedPCA`          | How the variance of a time-delay embedding is spread across its principal components.  |
| `NL_EVTLocalDim`       | Local dimension and persistence of the reconstructed attractor, from extreme-value statistics of close returns. |
| `NL_FNN`               | How the fraction of false nearest neighbors falls as the embedding dimension increases. |
| `NL_FractalDimensions` | Spectrum of generalized (fractal) dimensions of the delay embedding, `D(q)`.           |
| `NL_GPCorrSum`         | Correlation sum and its scaling with distance (Grassberger-Procaccia).                 |
| `NL_LargestLyap`       | Largest Lyapunov exponent, from how quickly initially close points diverge.            |
| `NL_LocalDensity`      | How densely the delay-embedded trajectory is sampled around each of its points.        |
| `NL_LyapSpec`          | Spectrum of Lyapunov exponents of the delay-embedded trajectory.                       |
| `NL_nlpe`              | Normalized drop-one-out constant interpolation nonlinear prediction error.             |
| `NL_NSAMDF`            | Nonlinearity measure from the nonlinear average magnitude difference function (nsAMDF). |
| `NL_PersistentHomology` | Topological data analysis (persistent homology) of a time-delay embedding.            |
| `NL_PoincareSection`   | Poincaré section analysis of a time series.                                            |
| `NL_RecurrenceTimes`   | Recurrence-time statistics from a recurrence plot.                                     |
| `NL_ReturnTime`        | Analysis of the histogram of return times.                                             |
| `NL_RQA`               | Recurrence quantification analysis (RQA) of the delay-embedded series.                 |
| `NL_SSA`               | Singular spectrum analysis (SSA): trend, oscillation, and separability structure.      |
| `NL_TakensEstimator`   | Takens' estimator of the correlation dimension.                                        |
| `NL_ZeroOneTest`       | The 0-1 test for chaos.                                                                |
| `NW_OrdinalPartitionNetwork` | Ordinal partition transition network measures.                                   |
| `PP_SchreiberDenoise`  | Nonlinear noise reduction, and how it changes the series.                              |
| `SD_MakeSurrogates`    | Generates surrogate time series (used internally by `SD_Surrogates`, `SD_SurrogateTest` and `SP_Bicoherence`). |
| `SD_SurrogateTest`     | Compares test statistics of a time series to those of its surrogates.                  |
| `SD_Surrogates`        | Surrogate data test of a nonlinear statistic, `tc3` or `trev`.                         |
| _**Fluctuation analysis:**_ |                                                                                     |
| `SC_FluctAnal`         | Scaling exponents from fluctuation analysis (including detrended fluctuation analysis), by a choice of methods. |
| `SC_HiguchiFD`         | Higuchi's fractal dimension of a time series.                                          |
| `SC_MFDFA`             | Multifractal detrended fluctuation analysis (MFDFA): the multifractal spectrum.         |
| `SC_MMA`               | Multiscale multifractal analysis (PhysioNet implementation).                           |

## Fourier and wavelet transforms, periodicity measures

Properties of the time-series power spectrum, wavelet spectrum, and other periodicity measures.

| Code file            | Description                                                               |
| ---------------------- | ------------------------------------------------------------------------------ |
| `SP_Summaries`       | Statistics of the power spectrum of a time series.                        |
| `SP_SummariesPhase`  | Statistics of the Fourier phase spectrum of a time series.                |
| `SP_Bicoherence`     | Quadratic phase coupling between frequencies, from the squared bicoherence. |
| `SP_Cepstrum`        | Harmonic (comb) structure of the power spectrum, from the real cepstrum.  |
| `SP_EnvelopeStats`   | Statistics of the amplitude envelope of the series and of its dominant oscillation. |
| `SP_PhaseAmpCoupling` | Cross-frequency phase-amplitude coupling between frequency bands.        |
| `SP_PhaseFluctuationScaling` | Multiscale fluctuation analysis of the instantaneous phase of the dominant oscillation. |
| `SP_Rhythmicity`     | Phase-persistence ("rhythmicity") spectrum, separating sustained oscillations from bursts. |
| `SP_SinusoidFit`     | Fits sinusoids or a Fourier series to the time series.                    |
| `SP_Specparam`       | Separates the power spectrum into aperiodic (1/f) and periodic (oscillatory) components. |
| `SP_SpectralTimeFreq`| Time-varying spectral statistics from a spectrogram.                      |
| `PD_PeriodicityWang` | Periodicity extraction measure of Wang et al. (2007).                     |
| `ST_PeakIntervals`   | Prominence and regularity of the peaks of a time series.                  |
| `WL_Coeffs`          | How quickly the sorted magnitudes of a wavelet detail signal decay from their maximum. |
| `WL_cwt`             | Statistics of the continuous wavelet transform of a time series.          |
| `WL_DetailCoeffs`    | How the size of wavelet detail signals varies across scales.              |
| `WL_DWTCoeff`        | Statistics of the discrete wavelet transform detail coefficients at each level. |
| `WL_fBM`             | Hurst exponent of the integrated time series, treated as fractional Brownian motion. |
| `WL_modwtvar`        | Multiscale variance decomposition via the maximal overlap DWT.            |
| `WL_scal2frq`        | Dominant wavelet level of a time series, and its pseudo-period and pseudo-frequency. |
| `WL_wpdBestTree`     | Adaptive best-basis wavelet packet decomposition.                         |

## Symbolic transformations

Properties of a discrete symbolization of a time series.

| Code file                | Description                                                          |
| --------------------------- | -------------------------------------------------------------------------- |
| `SB_BinaryGapHomogeneity` | Homogeneity of the gaps between like symbols in a binarization of the time series. |
| `SB_BinaryStats`         | Statistics on a binary symbolization of the time series.             |
| `SB_BinaryStats_AR1`     | Binary run-length statistics normalized against an AR(1) null.       |
| `SB_CoarseGrain`         | Coarse-grains a continuous time series to a discrete alphabet (used internally by `SB_MotifThree`, `SB_TransitionMatrix` and `FC_Surprise`). |
| `SB_MotifThree`          | Motifs in a coarse-graining of a time series to a 3-letter alphabet. |
| `SB_MotifTwo`            | Local motifs in a binary symbolization of the time series.           |
| `SB_TransitionMatrix`    | Transition probabilities between time-series states.                 |
| `SB_TransitionPAlphabet` | How transition probabilities change with alphabet size.              |

## Statistics from biomedical signal processing

Simple time-series properties derived mostly from the heart rate variability (HRV) literature.

| Code file        | Description                                             |
| ------------------ | ------------------------------------------------------------ |
| `MD_hrv_classic` | Classic heart rate variability (HRV) statistics.        |
| `MD_pNN`         | `pNNx` measures of heart rate variability.              |
| `MD_PolVar`      | The `POLVARd` measure of a time series.                 |
| `MD_Porta`       | Porta's symbolic-dynamics word-type indices.            |
| `MD_RawHRVMeas`  | Poincaré-plot heart rate variability (HRV) measures of a time series. |

## Basic statistics, trend

Basic statistics of a time series, including measures of trend.

| Code file          | Description                                                               |
| --------------------- | -------------------------------------------------------------------------- |
| `SY_Trend`         | Quantifies various measures of trend in a time series.                    |
| `ST_FitPolynomial` | Goodness of a polynomial fit to a time series.                            |
| `ST_Length`        | Length of a data vector.                                                  |
| `ST_LocalExtrema`  | How local maximums and minimums vary across the time series.              |
| `ST_MomentCorr`    | Correlations between simple statistics in local windows of a time series. |
| `ST_SimpleStats`   | Basic count-based statistics (e.g., proportion of zero-crossings or local extrema). |

## Others

Other properties, like extreme values, visibility graphs, physics-based simulations, and dependence on pre-processings applied to a time series.

| Code file            | Description                                                                |
| ----------------------- | -------------------------------------------------------------------------- |
| `EX_ExtremeEventOrder` | Temporal patterning of positive vs. negative extreme events.              |
| `EX_MovingThreshold` | Moving threshold model for extreme events in a time series.                |
| `NW_VisibilityGraph` | Visibility graph analysis of a time series.                                |
| `PH_ForcePotential`  | Statistics of a simulated particle in a potential well, pushed by the time series. |
| `PH_KramersMoyal`    | How the series' average drift and noise intensity depend on its current level (Kramers-Moyal coefficients). |
| `PH_Walker`          | How a simulated particle driven by the series moves, and how its path differs from the series. |
| `PP_Compare`         | How time-series properties change after a preprocessing step.              |
| `PP_Iterate`         | How time-series properties change as a preprocessing step is applied more and more strongly. |

## Retained in `Operations/` but not in the default feature set

These files are in the `Operations` directory (e.g., so that custom input files keep working), but are not used in the default feature set:

* `DN_Cumulants`: distributional moments of the input data.
* `DN_Fit_mle`: maximum likelihood distribution fit to data.
* `DT_IsSeasonal`: a simple test of seasonality (deprecated).
* `EN_Randomize`: how properties of the series change as it is progressively randomized.
* `HT_HypothesisTest`: statistical hypothesis tests (deprecated; split into `HT_MarginalTests` and `HT_IndependenceTests`).
* `IN_MutualInfo`: mutual information of two data vectors.
* `MF_GP_LocalPrediction`: how well a Gaussian process fitted to short windows predicts nearby held-out values.
* `NL_BoxCorrDim`: deprecated alias of `NL_BoxCountEntropyRate`.

## External code and toolboxes

The `Toolboxes` directory contains external code that some operations use:

* `ARFIT`: Schneider and Neumaier's ARfit package for fitting autoregressive models (used by `MF_arfit` and other model-fitting operations).
* `catch22`: the [_catch22_](https://github.com/DynamicsAndNeuralSystems/catch22) feature set, included as a git submodule. No operation in the default _hctsa_ feature set needs it, but the `'catch22'` feature set does (`TS_Init(...,'catch22')`); clone with `--recurse-submodules` to get it.
* `Danny_Kaplan`: Danny Kaplan's nonlinear statistics, registered directly as the `DK_crinkle`, `DK_theilerQ` and `DK_timerev` operations (with the helper `DK_lagembed`).
* `DVV_Toolbox`: the delay vector variance method of Gautama, Mandic and Van Hulle (used by `NL_DVV`).
* `gpml`: Rasmussen and Nickisch's Gaussian process code (used by `MF_GP_FitAcross` and `MF_GP_Hyperparameters`).
* `infodynamics-dist`: Joseph Lizier's Java Information Dynamics Toolkit (JIDT), used for mutual information estimates (via `IN_Initialize_MI`).
* `Max_Little`: Max Little's recurrence period density entropy code (used by `EN_rpde`) and step-detection toolkit (used by `CP_ML_StepDetect` and `CP_l1pwc_SweepLambda`).
* `Michael_Small`: Michael Small's code for complexity, entropy and nonlinear prediction (used by, e.g., `EN_LZComplexity`, `EN_Shannon` and `NL_nlpe`).
* `nsamdf`: the nonlinear average magnitude difference function (used by `NL_NSAMDF`).
* `Physionet`: PhysioNet's sample entropy code (used by `EN_SampEn` and `EN_MSE`).
* `ripser`: Ulrich Bauer's C++ code for persistent homology (used by `NL_PersistentHomology`).
* `Tisean_3.0.1`: the TISEAN nonlinear time-series analysis package (used by `NL_BoxCountEntropyRate`, `NL_c1`, `NL_d2`, `NL_Dimensions`, `NL_FNN`, `NL_GPCorrSum`, `NL_LargestLyap`, `NL_LyapSpec`, `PP_SchreiberDenoise` and `SY_nstat_z`).
* `ZG_hmm`: Zoubin Ghahramani's hidden Markov model code (used by `MF_hmm_CompareNStates` and `MF_hmm_Fit`).

The compiled components (mex files, TISEAN and ripser) are built by `install.m`, which runs `compile_mex.m` in this directory; it in turn calls `compile_tisean.m` and `compile_ripser.m`, which can also be re-run on their own.

Some operations also use Matlab toolboxes.
The Econometrics Toolbox is needed only by `SY_KPSStest`, `SY_PPtest`, `SY_VarRatioTest` and the Ljung-Box test of `HT_IndependenceTests` (7 master operations in the default feature set).
