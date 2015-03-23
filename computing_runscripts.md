## Cycling through computations using runscripts

This section on computation has outlined the three main steps to computation as:

1. Retrieving from the database (`TSQ_prepared`),
2. Calculating operations on time series (`TSQ_brawn`), and
3. Writing the results back to the database (`TSQ_agglomerate`).

In order to calculate across large sections of the database, it is convenient to use runscripts, that allow large computations to be broken up into smaller pieces, and using `for` loops to cycle through these pieces.
By designating different sections of the database to cycle through, this procedure can also be used to (manually) distribute the computation across different machines.
Retrieving a large section of the database at once can be problematic because it requires large disk reads and writes, uses a lot of memory, and if problems occur in the reading or writing to/from files, one may have to abandon a large number of existing computations.
Due to the ability of `TSQ_brawn` to parallelize the computation across multiple CPU processors, it is usually the most efficient practice to retrieve a small number of time series at each iteration of the `TSQ_prepared`–`TSQ_brawn`–`TSQ_agglomerate` loop.
Depending on their length, we usually retrieve a single time series at each iteration of a runscript loop.
An example runscript is given in the code that accompanies this document, as `sample_runscript.m`, and can be viewed as a template for runscripts that one may wish to use when performing time-series calculations across the database.