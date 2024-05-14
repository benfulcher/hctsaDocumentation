# Error handling and maintenance

In this section we describe how keywords and other types of metadata stored in the database can be manipulated, and how results of whole sets of operations and time series can be cleared or completely deleted from the database. These tasks are implemented as Matlab functions, and ensure that key database structures are maintained. Instead performing such tasks by acting directly on the database can cause inconsistencies and should be avoided.

## Java heap space

Running out of java heap space throws the error `java.lang.OutOfMemoryError`, and usually happens when trying to retrieve a large set of time series/operations from the database. Matlab needs to keep the whole retrieval in memory, and has a hard limit on this. The java heap size can be increased in the Matlab preferences, under _General_  _Java Heap Memory_.

