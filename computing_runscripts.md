# Cycling through computations using runscripts

As described in this section, computation involves three main steps:

1. Retrieving data from the database (using `TSQ_prepared`),
2. Calculating operations on time series (using `TSQ_brawn`), and
3. Writing the results back to the database (using `TSQ_agglomerate`).

In order to calculate across large sections of the database, it is convenient to use runscripts, that allow large computations to be broken up into smaller pieces, and using `for` loops to cycle through these pieces.
By designating different sections of the database to cycle through, this procedure can also be used to (manually) distribute the computation across different machines.
Retrieving a large section of the database at once can be problematic because it requires large disk reads and writes, uses a lot of memory, and if problems occur in the reading or writing to/from files, one may have to abandon a large number of existing computations.

It is usually the most efficient practice to retrieve a small number of time series at each iteration of the `TSQ_prepared`–`TSQ_brawn`–`TSQ_agglomerate` loop, and distribute this computation across multiple machines if possible.
An example runscript is given in the code that accompanies this document, as `sample_runscript.m`, which retrieves a single time series at a time, computes it, and then writes the results back to the database in a loop.
This can be viewed as a template for runscripts that one may wish to use when performing time-series calculations across the database.