# Running Computations
<!--{#sec:calculating}-->

In this section we describe our Matlab framework for computing the output of operations in the **Operations** table of the *mySQL* database on time series in the **TimeSeries** table of the database.

The procedure involves three main steps:

1. Retrieve a set of time series and operations from (the **Results** table) of the database to a local Matlab file, **HCTSA\_loc.mat** (use `SQL_retrieve`).

2. Compute the operations on the retrieved time series in Matlab and store the results locally (use `TS_compute`).

3. Write the results back to the **Results** table of the database (use `SQL_store`).

This computational workflow is represented schematically below.
A sample script to iterate over these steps is the `sample_runscript` is explained [here](computing_runscripts.md).

![**Computation workflow schematic.**](img/ComputationSchematic.png)

This workflow is very well suited to distributed computing for large datasets, whereby each node can iterate over a small set of time series, with all the results being written back to a central location (the *mySQL* database).