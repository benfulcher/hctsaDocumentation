# Dealing with errors
<!--{#sec:errors}-->

Errors in particular pieces of code can often occur when applying them to large numbers of time series. Some errors are not problems with the code, but problems with applying particular sets of code to particular time series, such as when a Matlab fitting function reaches the maximum number of iterations and returns an error.
These are kept and stored as errors.
Other errors are genuine problems with the code that need to be corrected.
Although we have attempted to preempt and deal with as many errors in the code as possible, ongoing feedback will be used to further refine and develop the code over time.
Note that errors returned from Matlab files are dealt with using `try-catch` statements, but errors with **mex** functions can produce a fault that crashes Matlab or the system.
These situations must be dealt with by either identifying and fixing the problem in the original source code and recompiling, or by removing the problem code.