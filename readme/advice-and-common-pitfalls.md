---
description: Thinking about running an hctsa analysis? Read this first.
---

# Advice and common pitfalls

The typical data analysis pipeline starts with inspecting and understanding the data, processing it in accordance with the questions of interest (and to be consistent with the assumptions of the analysis methods that will be applied), and then formulating and testing appropriate analysis methods. A typical _hctsa_ pipeline inverts this process: many analysis methods are first applied, and then their results are  interpreted.

Good practice involves thinking carefully about this full _hctsa_ pipeline, including the type of questions and interpretations that are sought from it, and thus how the data are to be prepared, and how the results can be interpreted accurately.

## Data processing

_hctsa_ contains thousands of time-series analysis methods, many of which make strong assumptions of the data, such as that it is (weak-sense) stationary. Although _hctsa_ has been applied meaningfully to short, non-stationary patterns (as commonly studied in time-series data-mining applications), it is better suited to longer streams of data, from which more subtle temporal statistics can be sampled.

_hctsa_ is not substitute for thoughtful domain-motivated data preparation and processing. For example, _hctsa_ cannot know is 'signal' and what is 'noise' for the question you're asking of your data. The analyst should prepare their data in a way that makes sense for the questions of relevance to them, including the possibility of de-trending/filtering the data, applying noise-removal methods, etc.

The following should be considered:

* _**Standardizing.**_ If your time-series data are measured on scales that differ across different recordings, then the data should be appropriately standardized.
* _**Detrending.**_ If your data contain low-order trends that are not meaningful (e.g., sensor drift) or not a signal of relevance (your question is based more around the structure of deviations from a low-frequency trend), then this should be removed.
* _**Denoising**_. If your data contain high-frequency measurement noise, the analyst should consider removing it. For example, using a filter (e.g., moving average), wavelet denoising, or using a phase-space reconstruction (e.g., cf. [Schreiber's method](https://link.aps.org/doi/10.1103/PhysRevE.47.2401)).
* _**Downsampling**_. Features assume that the data are sampled at a rate that properly resolves the temporal patterns of interest. If your data are over-sampled, then many features will be sensitive to this dominating autocorrelation structure, and will be less sensitive to interesting patterns in the data. In this case, you can consider downsampling your data, for which there are many heuristics (e.g., cf. [Toker et al.](https://www.nature.com/articles/s42003-019-0715-9)).

## Interpreting Features

#### Checking for simpler explanations

There are often many routes to solving a given data analysis challenge. For example, in a time-series classification problem, the two classes may be perfectly distinguished based on their lag-1 autocorrelation, and also on their Lyapunov exponent spectrum, and also on hundreds of other properties. In general, one should avoid interpreting the most complex features (like Lyapunov exponents) as being uniquely useful for a problem, as they reproduce the behavior of much simpler features, which provide a more interpretable and parsimonious interpretation of the relevant patterns in the dataset. For other problems, time-series analysis methods (that are sensitive to the time-ordering of the data samples) may not provide any benefit at all over properties of the data distribution (e.g., the variance), or more trivial differences in time-series length across classes.

In general, **complex explanations of patterns in a dataset can only be justified when simpler explanations have been ruled out**. E.g., Do not write a paper about a complex (e.g., powerlaw fit to a visibility graph degree-distribution) feature when your data can be just as well (or better) distinguished by their variance.

_hctsa_ has many functions to check this type of behavior: from inspecting the pairwise correlation structure of high-performing features (in `TS_TopFeatures`) to basic checks on different keyword-labeled classes of features (in `TS_CompareFeatureSets`).

