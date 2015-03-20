## Running Computations {#sec:calculating}


### Cycling through calculations using runscripts {#sec:runscripts}

This section on computation has outlined the three main steps to computation as:

1. Retrieving from the database (`TSQ_prepared`),
2. Calculating operations on time series (`TSQ_brawn`), and
3. Writing the results back to the database (`TSQ_agglomerate`).

In order to calculate across large sections of the database, it is convenient to use runscripts, that allow large computations to be broken up into smaller pieces, and using `for` loops to cycle through these pieces.
By designating different sections of the database to cycle through, this procedure can also be used to (manually) distribute the computation across different machines.
Retrieving a large section of the database at once can be problematic because it requires large disk reads and writes, uses a lot of memory, and if problems occur in the reading or writing to/from files, one may have to abandon a large number of existing computations.
Due to the ability of `TSQ_brawn` to parallelize the computation across multiple CPU processors, it is usually the most efficient practice to retrieve a small number of time series at each iteration of the prepared–brawn–agglomerate loop.
Depending on their length, we usually retrieve five time series at each iteration of a runscript loop.
An example runscript is given in the code that accompanies this document, as `sample_runscript.m`, and can be viewed as a template for runscripts that one may wish to use when performing time-series calculations across the database.

### Dealing with errors {#sec:errors}

Errors in particular pieces of code can often occur when applying them to large numbers of time series. Some errors are not problems with the code, but problems with applying particular sets of code to particular time series, such as when a Matlab fitting function reaches the maximum number of iterations and returns an error.
These are kept and stored as errors.
Other errors are genuine problems with the code that need to be corrected.
Although we have attempted to preempt and deal with as many errors in the code as possible, ongoing feedback will be used to further refine and develop the code over time.
Note that errors returned from Matlab files are dealt with using `try-catch` statements, but errors with `mex` functions can produce a fault that crashes Matlab or the system.
These situations must be dealt with by either identifying and fixing the problem in the original source code and recompiling, or by removing the problem code.