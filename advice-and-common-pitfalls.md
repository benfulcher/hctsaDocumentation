---
description: Thinking about running an hctsa analysis? Read this first.
---

# Advice and common pitfalls

### Data processing

_hctsa_ contains thousands of time-series analysis methods, many of which make strong assumptions of the data, such as that it is \(weak-sense\) stationary. Although _hctsa_ has been applied meaningfully to short, non-stationary patterns \(as commonly studied in time-series data-mining applications\), it is better suited to longer streams of data, from which more subtle temporal statistics can be sampled.

_hctsa_ is not substitute for thoughtful domain-motivated data preparation and processing. For example, _hctsa_ cannot know is 'signal' and what is 'noise' for the question you're asking of your data. The analyst should prepare their data in a way that makes sense for the questions of relevance to them, including the possibility of de-trending/filtering the data, applying noise-removal methods, etc.

For example:

* If your time-series data are measured on scales that differ across different recordings, then the data should be appropriately standardized
* If your data contain low-order trends that are not meaningful \(e.g., sensor drift\) or not a signal of relevance \(your question is based more around the structure of deviations from a low-frequency trend\), then this should be removed.
* If your data contain high-frequency measurement noise, the analyst should consider removing it. For example, using a filter \(e.g., moving average\), wavelet denoising, or using a phase-space reconstruction \(cf. [Schreiber's method](https://link.aps.org/doi/10.1103/PhysRevE.47.2401)\).

### Interpreting Features

#### Checking for simpler explanations

There are often many routes to solving a given data analysis challenge. For example, in a time-series classification problem, the two classes may be perfectly distinguished based on their lag-1 autocorrelation, and also on their Lyapunov exponent spectrum, and also on hundreds of other properties. In general, one should avoid interpreting the most complex features \(like Lyapunov exponents\) as being uniquely useful for a problem, as they reproduce the behavior of much simpler features, which provide a more interpretable and parsimonious interpretation of the relevant patterns in the dataset. For other problems, time-series analysis methods \(that are sensitive to the time-ordering of the data samples\) may not provide any benefit at all over properties of the data distribution \(e.g., the variance\), or more trivial differences in time-series length across classes.

In general, **complex explanations of patterns in a dataset can only be justified when simpler explanations have been ruled out**. E.g., Do not write a paper about a complex \(e.g., powerlaw fit to a visibility graph degree-distribution\) feature when your data can be just as well \(or better\) distinguished by their variance.

_hctsa_ has many functions to check this type of behavior: from inspecting the pairwise correlation structure of high-performing features \(in `TS_TopFeatures`\) to basic checks on different keyword-labeled classes of features \(in `TS_CompareFeatureSets`\).

\_\_

