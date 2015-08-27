# *mySQL* database

When running large-scale *hctsa* computations, it can be useful to set up a *mySQL* database for time series, operations, and the computation results, and have many Matlab instances (running on different nodes of a compute cluster, for example) communicate directly with the database.

The *hctsa* software comes with this functionality, allowing a more powerful, distributed way to compute and store results of a large-scale computation.

This section outlines the steps involved in setting up, and running hctsa computations using a linked *mySQL* database.
