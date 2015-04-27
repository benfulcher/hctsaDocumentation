# Retrieving results from the database using `SQL_retrieve`

The first step of any analysis is to retrieve a relevant portion of data from the *mySQL* database to local Matlab files for analysis.
This is done using the `SQL_retrieve` function described [earlier](retrieving_to_compute.md), except we use the `'all'` input to retrieve all data, rather than the `'null'` input used to retrieve just missing data (requiring calculation).

Example usage is as follows:

    >> SQL_retrieve(ts_ids, op_ids,'all');

for vectors `ts_ids` and `op_ids`, specifying the **ts\_id**s and **op\_id**s to be retrieved from the database.

Sets of **ts_id**s and **op_id**s to retrieve can be selected by inspecting the database, or by retrieving relevant sets of keywords using the `SQL_getids` function.
Running the code in this way, using the ‘all’ tag, ensures that the full range of **ts\_id**s and **op\_id**s specified are retrieved from the database and stored in the local file, **HCTSA_loc.mat**, which can then form the basis of subsequent analysis.

The database structure provides much flexibility in storing and indexing the large datasets that can be analyzed using the *hctsa* approach, however the process of retrieving and storing large amounts of data from a database can take a considerable amount of time, depending on database latencies.

## Grouping elements using `TS_LabelGroups`
<!--{#sec:grouping_variables}-->

Highly comparative analyses often involve classification tasks, in which each observation is assigned a (numeric) class label.
Once data has been retrieved, as described above, class labels can be assigned to each time series in a dataset, and stored in the local **HCTSA_*.mat** files using the function `TS_LabelGroups`.

The example below assigns labels to two groups of time series in the **HCTSA_loc.mat** (specifying `'orig'`), corresponding to those labeled as 'parkinsons' and those labeled as 'healthy':

    keywordGroups = {'parkinsons','healthy'};
    saveBack = 1; % save grouping info back to the HCTSA_loc.mat file
    TS_LabelGroups('orig',keywordGroups,'ts',saveBack);

The second input is a cell specifying the keyword string for each group.
This can be done as above to select all examples matching the keyword constraints, or as follows to specify how many should be labeled in each group:

    keywordGroups = {'parkinsons',50;'healthy',50};
    TS_LabelGroups('orig',keywordGroups,'ts',1);

This example labels 50 random examples of each class, using the first column to specify the keyword strings for each group, and the second column to specify the number of each label to include (using `'0'` used to specify *all*).

The final input, `saveBack` (=1 by default), saves the group indices back to the data file, in the above example, the **TimeSeries** structure array (in **HCTSA_loc.mat**) will be given a new field, **Group**, that contains the group index of each time series.
Group indices stay with the time series they are assigned to, e.g., after filtering and normalizing the data (using `TS_normalize`) and clustering the data (using `TS_cluster`) - the same group labels will stay with the time series.
These labels are used by a range of analysis functions (including `TS_plot_pca`).

Group labels can be reassigned at any time by re-running the `TS_LabelGroups` function.