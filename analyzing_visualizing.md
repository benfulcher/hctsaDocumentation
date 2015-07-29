# Performing highly comparative analysis
<!--{#sec:analyzing}-->

Once a set of operations have been computed on a time-series dataset, the results are stored in a local **HCTSA_loc.mat** file (which is produced either directly, using `TS_init` and `TS_compute`, or by retrieving computed results from a *mySQL* database using `SQL_retrieve`, described [here](retrieving.md)).
The result can be used to perform a wide variety of highly comparative analyses, such as those outlined in [our paper](http://rsif.royalsocietypublishing.org/content/10/83/20130048.full).

The types of analysis employed should the suit specific time-series analysis tasks of interest.
Setting up the problem, guiding the methodology, and interpreting the results requires strong scientific input that should draw on domain knowledge, including the questions asked of the data, experience performing data analysis, and statistical considerations.

Users can be creative in their exploration and analysis of the data, or draw upon a library of basic analytic techniques that we have developed.

The first main component of an *hctsa* analysis involves filtering and normalizing the data using `TS_normalize`, described [here](filtering_and_normalizing.md), which produces a file called **HCTSA_N.mat**.
Information about the similarity of pairs of time series and operations can be computed using `TS_cluster`, which stores this information in **HCTSA_N.mat**.
The suite of plotting and analysis tools that we provide with *hctsa* by default work with this normalized data, stored in **HCTSA_N.mat**.