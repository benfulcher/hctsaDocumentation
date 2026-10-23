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

You can specify the classification algorithm by setting `cfnParams.whatClassifier` and passing `cfnParams` to `TS_Classify` (the default, set by `GiveMeDefaultClassificationParams`, is `'svm-linear-lowdim'`).
You can see the options in `GiveMeCfn`.
Typically because there is complexity in the embedding in a high-dimensional feature space, we have tried to remove complexity in the classifiers (to avoid overfitting), also for interpretability.
You can also export the features using `OutputToCSV` and analyze them in other environments (like Python).

</details>

<details>

<summary><strong>Can I perform a multivariate time series analysis using </strong><em><strong>hctsa</strong></em><strong>?</strong></summary>

_hctsa_ is designed to extract thousands of features from a _**single univariate time series**_; [pyspi](https://github.com/DynamicsAndNeuralSystems/pyspi) implements hundreds of pairwise dependence measures from multivariate time series.

You could incorporate both (i) univariate features of the components of the system; and (ii) features summarizing the pairwise dependence structure of the distributed system.

For example, some points to consider:

* You could compute univariate features of each component of your system and then concatenate them to combine features of all individual time series:
  * To do this you may consider using a reduced set of features, like [catch22](https://github.com/DynamicsAndNeuralSystems/catch22) to avoid massive dimensionality explosion.
  * You may alternatively use _hctsa_ and then do feature selection or dimensionality reduction to tailor a reduced feature set to your application.
  * You may also consider adding some pairwise dependence measures to summarize the multivariate structure, cf. [pyspi](https://github.com/DynamicsAndNeuralSystems/pyspi).
* You could compute univariate features of extracted dominant components of your multivariate system, e.g., using PCA.

</details>

<details>

<summary><strong>How can I export the extracted features?</strong></summary>

Use `OutputToCSV`. This gives you `.csv` files corresponding to a given _hctsa_ calculation that you can analyze however you please.

</details>

<details>

<summary><strong>Can I use <em>hctsa</em> features without MATLAB (e.g., in Python, R or Julia)?</strong></summary>

There are a few options for working without MATLAB:

* Use the Python package [_pyhctsa_](https://github.com/DynamicsAndNeuralSystems/pyhctsa), where future development is planned to happen.
* Use an alternative (e.g., native R or native Python) feature-extraction tool, such as those listed [here](related-time-series-resources.md).
  These have far fewer features than _hctsa_ but can get you some of the way there.
* Use a reduced feature set coded in C, which can be used efficiently from many programming languages: [_catch22_](https://github.com/DynamicsAndNeuralSystems/catch22) (22 features selected for general time-series classification) or [_catchaMouse16_](https://github.com/DynamicsAndNeuralSystems/catchaMouse16) (16 features selected for fMRI).
  You can also derive a reduced set of useful features for your own problem with _hctsa_, and then implement them (or find non-MATLAB implementations).

Note also the licenses: the _hctsa_ framework for running analyses and visualizations is licensed as [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/) (a commercial license is available from [Engine Analytics](http://www.engineanalytics.org/)), and the code for computing features is licensed as [GPL v3](https://www.gnu.org/licenses/gpl-3.0.en.html).

</details>

***
