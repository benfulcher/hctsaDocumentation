---
description: Some advice for getting started with an hctsa analysis
---

# Getting started

## Preparing for an analysis

_hctsa_ automates some parts of a time-series analysis pipeline (such as guiding the selection of informative statistical properties in a time-series classification problem), but it does not replace expertise. Careful thought, with understanding of the problem and domain-specific issues are essential in designing how _hctsa_ can be used for a given application, and in properly interpreting its results.

Some general advice before embarking on an _hctsa_ analysis:

1. **What are the data?**
   * For long, streaming data: **how long** is each time series? _Does this capture the timescale of the problem you care about?_
   * For continuously evolving processes: At **what rate are the data sampled**? _Does this capture the patterns of interest?_ E.g., if the sampling rate is too high, this can lead to trivially autocorrelated time series such that time-series methods in _hctsa_ will find it challenging to resolve the patterns of interest.
   * Are the **appropriately processed** (detrended, artifacts removed, …)? This requires careful thought, often with domain expertise, about what problem is being solved. E.g., many time-series analysis algorithms will be dominated by underlying trends if underlying trends in time series are not removed. In general, properties of the data, especially if they're likely to affect many time-series analysis algorithms (like underlying trends, artefactual outliers, etc.) should be removed if they're not informative of the differences you care about distinguishing.
   * **What do they look like?** Addressing the questions above requires you to look at each of the time series to get a sense of the dynamics you're interested in characterizing using time-series features.
2. **What problem are you trying to solve?** In designing an analysis using _hctsa_, you will need to think about the sample size you have, what effect sizes are expected, what statistical power will you have, etc. E.g., if you only have 5 examples of each of two classes, you will not have the statistical power to pick out individual features from a library of 7000, simple (unregularized) classifiers will be likely to overfit, etc.
3. **Trial run with a reduced set**. Once you’ve devised a pipeline, it's best to run through it in _hctsa_ but using a _reduced feature set first_ (e.g., the catch22 set), which runs quickly on a laptop and gives you a sense for the process. Once you're satsified with the analysis pipeline, you can always scale up to the full _hctsa_ library of >7000 features.

## Example _hctsa_ Analysis Pipelines

Sometimes it's easiest to quickly get going when you can follow on to some example code. Here are some repositories that allow you to do just this.

### EEG

An overview tutorial on applying _hctsa_ to a 5-class EEG dataset is in [this GitHub repo](https://github.com/benfulcher/hctsaTutorial\_BonnEEG) (including the use of reduced feature set, _catch22_, within the _hctsa_ framework). There is also a recording of the tutorial (final \~hour is a hands-on demo using this dataset) on [YouTube](https://www.youtube.com/watch?v=YwPX3rWxP\_Y).

![](<../.gitbook/assets/Screen Shot 2022-05-31 at 08.39.56.png>)

### Flies in a tube

You can play with _hctsa_ using this [fly movement dataset and analysis code](https://github.com/benfulcher/hctsa\_phenotypingFly). This repository allows you to skip the process of running an _hctsa_ calculation (you can download pre-computed results), and get straight down to following some analyses, including code for classifying recordings during the day versus night, or from males versus females using time-series feature extraction.

### Worms in a dish

You can try your hand at classifying different strains of the nematode worm _C. elegans_ based on their time series of their movement speed. The repository, with links to pre-computed `HCTSA.mat` files, is [here](https://github.com/benfulcher/hctsa\_phenotypingWorm).
