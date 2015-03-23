# Retrieving results from the database using `TSQ_prepared`

The first step of any analysis is to retrieve a relevant portion of data from the *mySQL* database to local Matlab files for analysis.
This is done using the `TSQ_prepared` function described [earlier](retrieving_calculating_writing.md).
Example usage is provided:

    TSQ_prepared(ts_ids, op_ids,'all');

for vectors `ts_ids` and `op_ids`, specifying the **ts\_id**s and **op\_id**s to be retrieved from the database.
Sets of **ts_id**s and **op_id**s to retrieve can be selected by inspecting the database, or by retrieving relevant sets of keywords using the `SQL_getids` function.
Running the code in this way, using the ‘all’ tag, ensures that the full range of **ts\_id**s and **op\_id**s specified are retrieved from the database and stored in the local file, **HCTSA_loc.mat**, which can then form the basis of subsequent analysis.

The database structure provides much flexibility in storing and indexing the large datasets that can be analyzed using the *hctsa* approach, however the process of retrieving and storing large amounts of data from a database can take a considerable amount of time, depending on database latencies.