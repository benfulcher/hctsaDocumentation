# Performing highly comparative analysis
<!--{#sec:analyzing}-->

Once a set of operations have been computed on a time-series dataset, the results are stored in a local **HCTSA_loc.mat** file (which is produced either directly, using `TS_init` and `TS_compute`, or by retrieving computed results from a *mySQL* database using `SQL_retrieve`, described [here](retrieving.md)).
The result can be used to perform a wide variety of highly comparative analyses, such as those outlined in [our paper](http://rsif.royalsocietypublishing.org/content/10/83/20130048.full).

The types of methods employed on a given dataset should be developed to suit specific time-series analysis tasks.
Setting up the problem, guiding the methodology, and interpreting the results requires strong scientific input that should draw on domain knowledge, including the questions asked of the data, experience performing data analysis, and statistical considerations.

Users can be creative in their exploration and analysis of the data, or draw upon a library of analytic techniques that we have developed.

The two main components of an *hctsa* analysis pipeline are:

2. Filtering and normalizing the data using `TS_normalize`, described [here](filtering_and_normalizing.md).