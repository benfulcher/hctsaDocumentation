# Performing highly comparative analysis
<!--{#sec:analyzing}-->

Once the database has been set up, populated with data and operations, and the operations have been computed on the time-series data, the result can be used to perform a wide variety of highly comparative analyses, such as those outlined in [our paper](http://rsif.royalsocietypublishing.org/content/10/83/20130048.full).

The types of methods employed on a given dataset should be developed to suit specific time-series analysis tasks.
Setting up the problem, guiding the methodology, and interpreting the results requires strong scientific input that should draw on domain knowledge, including the questions asked of the data, experience performing data analysis, and statistical considerations.

Once a highly comparative dataset is produced, users can be creative in their exploration and analysis of the data, or draw upon a library of analytic techniques that we have developed.

The two main components of an *hctsa* analysis pipeline are:
1. Retrieving results from the database using `TSQ_prepared`, described [here](retrieving.md).
2. Filtering and normalizing the data using `TSQ_normalize`, described [here](filtering_and_normalizing.md).


### Grouping elements using `TSQ_LabelGroups`
<!--{#sec:grouping_variables}-->

Analyses often involve classification tasks, in which each observation is assigned a (numeric) class label.
For a given analysis, this metadata can be assigned to each time series in a dataset, and stored in the local **HCTSA_*.mat** files using the function `TSQ_LabelGroups`.
Throughout our analysis, it is often important to incorporate grouped structure particularly between time series in a classification dataset, for example.
This is done using the function `TSQ_LabelGroups`.