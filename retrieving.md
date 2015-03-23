# Retrieving results from the database using `TSQ_prepared`

The first step of any analysis is to retrieve a relevant portion of data from the *mySQL* database to local Matlab files for analysis.
This is done using the `TSQ_prepared` function described [earlier](retrieving_to_compute.md), except we use the `'all'` input to retrieve all data, rather than the `'null'` input used to retrieve just missing data (requiring calculation).

Example usage is as follows:

    TSQ_prepared(ts_ids, op_ids,'all');

for vectors `ts_ids` and `op_ids`, specifying the **ts\_id**s and **op\_id**s to be retrieved from the database.

Sets of **ts_id**s and **op_id**s to retrieve can be selected by inspecting the database, or by retrieving relevant sets of keywords using the `SQL_getids` function.
Running the code in this way, using the ‘all’ tag, ensures that the full range of **ts\_id**s and **op\_id**s specified are retrieved from the database and stored in the local file, **HCTSA_loc.mat**, which can then form the basis of subsequent analysis.

The database structure provides much flexibility in storing and indexing the large datasets that can be analyzed using the *hctsa* approach, however the process of retrieving and storing large amounts of data from a database can take a considerable amount of time, depending on database latencies.

## Grouping elements using `TSQ_LabelGroups`
<!--{#sec:grouping_variables}-->

Highly comparative analyses often involve classification tasks, in which each observation is assigned a (numeric) class label.
Once data has been retrieved, as described above, group labels can be assigned to each time series in a dataset, and stored in the local **HCTSA_*.mat** files using the function `TSQ_LabelGroups`.
The group labels can be reassigned at any time.

        groupIndices = TSQ_LabelGroups('orig',{'parkinsons',0;'healthy',0},'ts',1)
Throughout our analysis, it is often important to incorporate grouped structure particularly between time series in a classification dataset, for example.
This is done using the function `TSQ_LabelGroups`.