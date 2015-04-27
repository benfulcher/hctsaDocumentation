# Performing highly comparative analysis
<!--{#sec:analyzing}-->

Once the database has been set up, populated with data and operations, and the operations have been computed on the time-series data, the result can be used to perform a wide variety of highly comparative analyses, such as those outlined in [our paper](http://rsif.royalsocietypublishing.org/content/10/83/20130048.full).

The types of methods employed on a given dataset should be developed to suit specific time-series analysis tasks.
Setting up the problem, guiding the methodology, and interpreting the results requires strong scientific input that should draw on domain knowledge, including the questions asked of the data, experience performing data analysis, and statistical considerations.

Once a highly comparative dataset is produced, users can be creative in their exploration and analysis of the data, or draw upon a library of analytic techniques that we have developed.

The two main components of an *hctsa* analysis pipeline are:
1. Retrieving results from the database using `SQL_retrieve`, described [here](retrieving.md).
2. Filtering and normalizing the data using `TS_normalize`, described [here](filtering_and_normalizing.md).