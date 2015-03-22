## Performing highly comparative analysis
<!--{#sec:analyzing}-->

Once the database has been set up, populated with data and operations, and the operations have been computed on the time-series data, the result can be used to perform a wide variety of highly comparative analyses, such as those outlined in [our paper][hctsa-paper].

The types of methods employed on a given dataset should be developed to suit specific time-series analysis tasks.
Setting up the problem, guiding the methodology, and interpreting the results requires strong scientific input that should draw on domain knowledge, the questions asked of the data, experience performing data analysis, and statistical considerations.
Once a highly comparative dataset is produced, users can be creative in their exploration and analysis of the data, or draw upon a library of analytic techniques that we have developed.

The first step of any analysis is to retrieve a relevant portion of data from the database to local Matlab files for analysis.
As described above, this is done using the `TSQ_prepared` function.
Example usage is provided:

    TSQ_prepared(ts_ids, op_ids,'all');

for vectors `ts_ids` and `op_ids`, specifying the **ts\_id**s and **op\_id**s to be retrieved from the database. Running the code in this way, using the ‘all’ tag, ensures that the full range of **ts\_id**s and **op\_id**s specified are retrieved from the database and stored in the local file, **HCTSA_loc.mat**, which can then form the basis of subsequent analysis.


### Grouping elements {#sec:grouping_variables}

Throughout our analysis, it is often important to incorporate grouped structure particularly between time series in a classification dataset, for example.
This is done using `TSQ_LabelGroups`.

## Visualizing {#sec:visualizing}

There are many tasks that involve understanding the rich structure
contained in data matrices by visualizing them. In this section we
describe some basic tools we have developed to visualize the behavior of
time series and operations in the data matrix.

### Visualizing the data matrix using `TSQ_plot_DataMatrix` {#sec:visDatamatrix}

    TSQ_plot_DataMatrix

[[mention how keywords can be used using the `TSQ_LabelGroups`]]
[[should include the data matrix visualization code, dimensionality
reduction code, and greedy feature selection code, as examples, each
with example workflows starting from retrieval from the database]]
[[mention how do similarity search, etc.]]