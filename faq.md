---
description: Frequently asked questions pertaining to the use of hctsa.
cover: >-
  https://images.unsplash.com/photo-1557318041-1ce374d55ebf?crop=entropy&cs=srgb&fm=jpg&ixid=M3wxOTcwMjR8MHwxfHNlYXJjaHwxfHxxdWVzdGlvbnN8ZW58MHx8fHwxNzE1NTc1OTQyfDA&ixlib=rb-4.0.3&q=85
coverY: -114.91201224177506
---

# FAQ

Select a drop-down for more information:

<details>

<summary><strong>How can I use more machine-learning algorithms rather than SVM linear only using <code>TS_Classify</code>?</strong></summary>

You can specify the classification algorithm in the `cfnParams` structure. You can see the options in `GiveMeCfn`. Typically because there is complexity in the embedding in a high-dimensional feature space, we have tried to remove complexity in the classifiers (to avoid overfitting), also for interpretability. You can also use `OutputToCSV` and use the hctsa data in other environments (like python), \[if this route, feel free to share your python workflow [here](https://github.com/benfulcher/hctsaAnalysisPython)]

</details>

<details>

<summary><strong>Can I perform a multivariate time series analysis using </strong><em><strong>hctsa</strong></em><strong>?</strong></summary>

_hctsa_ is designed to extracting thousands of features from a _**single univariate time series**_; [pyspi](https://github.com/olivercliff/pyspi) implements hundreds of pairwise dependence measures from multivariate time series.

You could incorporate both (i) univariate features of the components of the system; and (ii) features summarizing the pairwise dependence structure of the distributed system.

For example, some points to consider:

* You could compute univariate features of each component of your system and then concatenate them to combine features of all individual time series:
  * To do this you may consider using a reduced set of features, like [catch22](https://github.com/DynamicsAndNeuralSystems/catch22) to avoid massive dimensionality explosion.
  * You may alternatively use _hctsa_ and then do feature selection or dimensionality reduction to tailor a reduced feature set to your application.
  * You may also consider adding some pairwise dependence measures to summarize the multivariate structure, cf. [pyspi](https://github.com/olivercliff/pyspi).
* You could compute univariate features of extracted dominant components of your multivariate system, e.g., using PCA.

</details>

<details>

<summary><strong>How can I export the extracted features?</strong></summary>

Use `OutputToCSV`. This gives you `.csv` files corresponding to a given _hctsa_ calculation that you can analyze however you please.

</details>

<details>

<summary><strong>Are there any plans to move away from Matlab (which is proprietary and thus not accessible to users perhaps outside universities) to perhaps Python/R/Julia?</strong></summary>

Most users are within Universities with a Matlab license, so I haven't come across issues with this (and the main analysis code is licensed non-commercial anyway). But there are a couple solutions if this comes up:

* Use an alternative (e.g., native R or native python) feature-extraction tool, such as those listed [here](https://github.com/benfulcher/hctsa/wiki/Related-time-series-resources). These have far fewer features than _hctsa_ but can get you some of the way there.
* Use Matlab temporarily to derive a reduced set of useful features for your problem, and then implement them (or find non-Matlab implementations). This pipeline is demonstrated (and implemented) in [catch22](https://github.com/DynamicsAndNeuralSystems/catch22) and we currently have new reduced ones in development. Our goal is to code reduced feature sets in C so they can be efficiently used in any programming language.
* Also note that although it doesn't get around the license issue, you can run _hctsa_ from python using [pyopy](https://github.com/strawlab/pyopy).

</details>

***
