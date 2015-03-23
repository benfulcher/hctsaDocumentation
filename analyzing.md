# Performing highly comparative analysis
<!--{#sec:analyzing}-->

Once the database has been set up, populated with data and operations, and the operations have been computed on the time-series data, the result can be used to perform a wide variety of highly comparative analyses, such as those outlined in [our paper](http://rsif.royalsocietypublishing.org/content/10/83/20130048.full).

The types of methods employed on a given dataset should be developed to suit specific time-series analysis tasks.
Setting up the problem, guiding the methodology, and interpreting the results requires strong scientific input that should draw on domain knowledge, including the questions asked of the data, experience performing data analysis, and statistical considerations.

Once a highly comparative dataset is produced, users can be creative in their exploration and analysis of the data, or draw upon a library of analytic techniques that we have developed.

The main components of an *hctsa* analysis pipeline are:
1. Retrieving results from the database using `TSQ_prepared` 

## Retrieving results from the database using `TSQ_prepared`

The first step of any analysis is to retrieve a relevant portion of data from the *mySQL* database to local Matlab files for analysis.
This is done using the `TSQ_prepared` function described [earlier](retrieving_calculating_writing.md).
Example usage is provided:

    TSQ_prepared(ts_ids, op_ids,'all');

for vectors `ts_ids` and `op_ids`, specifying the **ts\_id**s and **op\_id**s to be retrieved from the database.
Sets of **ts_id**s and **op_id**s to retrieve can be selected by inspecting the database, or by retrieving relevant sets of keywords using the `SQL_getids` function.
Running the code in this way, using the ‘all’ tag, ensures that the full range of **ts\_id**s and **op\_id**s specified are retrieved from the database and stored in the local file, **HCTSA_loc.mat**, which can then form the basis of subsequent analysis.

This process can take a considerable amount of time for large datasets, and depends on the latencies of the database.

### Grouping elements using `TSQ_LabelGroups`
<!--{#sec:grouping_variables}-->

Analyses often involve classification tasks, in which each observation is assigned a (numeric) class label.
For a given analysis, this metadata can be assigned to each time series in a dataset, and stored in the local **HCTSA_*.mat** files using the function `TSQ_LabelGroups`.
Throughout our analysis, it is often important to incorporate grouped structure particularly between time series in a classification dataset, for example.
This is done using the function `TSQ_LabelGroups`.