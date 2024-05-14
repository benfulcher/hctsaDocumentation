# Retrieving data from the database

The first step of any analysis is to retrieve a relevant portion of data from the _mySQL_ database to local Matlab files for analysis. This is done using the `SQL_Retrieve` function described [earlier](retrieving_to_compute.md), except we use the `'all'` input to retrieve all data, rather than the `'null'` input used to retrieve just missing data \(requiring calculation\).

Example usage is as follows:

> > SQL\_Retrieve\(ts\_ids, op\_ids,'all'\);

for vectors `ts_ids` and `op_ids`, specifying the **ts\_id**s and **op\_id**s to be retrieved from the database.

Sets of **ts\_id**s and **op\_id**s to retrieve can be selected by inspecting the database, or by retrieving relevant sets of keywords using the `SQL_GetIDs` function. Running the code in this way, using the ‘all’ tag, ensures that the full range of **ts\_id**s and **op\_id**s specified are retrieved from the database and stored in the local file, **HCTSA.mat**, which can then form the basis of subsequent analysis.

The database structure provides much flexibility in storing and indexing the large datasets that can be analyzed using the _hctsa_ approach, however the process of retrieving and storing large amounts of data from a database can take a considerable amount of time, depending on database latencies.

Note that missing, or NULL, entries in the database are converted to NaN entries in the local Matlab matrices.

