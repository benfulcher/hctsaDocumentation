# Inspecting errors using `TS_InspectQuality`

Errors in particular pieces of code can often occur when applying them to large numbers of time series.
Some errors are not problems with the code, but represent issues with applying particular sets of code to particular time series, such as when a Matlab fitting function reaches the maximum number of iterations and returns an error.
Other errors are genuine problems with the code that need to be corrected.
Both cases are labeled as errors in our framework.

Operations that produced errors, or other special values (such as NaNs, Infs, etc.), can be inspected using the `TS_InspectQuality` function (which loads in data from **HCTSA_loc.mat**).
This can be run in different modes:
* `'summary'` (default): shows only operations that produced special-valued outputs, summarizing the proportion of each such operation's outputs that correspond to each type of special-valued output
* `'full'`: shows the full data matrix, plotting the labels of each special-valued output
* `'reduced'`: show the full data matrix, but containing only operations that had special-valued outputs.

For example, running `TS_InspectQuality('summary')` loads in data from **HCTSA_loc.mat** and produces the following, which can be zoomed in on and explored to understand which features are producing problematic outputs:

![pca_image](img/InspectQuality.png)

## Errors with compiled code
Note that errors returned from Matlab files do not halt the progress of the computation (using `try-catch` statements), but errors with compiled **mex** functions (or external commandline packages like TISEAN) can produce a fault that crashes Matlab or the system.
We have performed some basic testing on all mex functions, but for some unusual time series, such faults may still occur.
These situations must be dealt with by either identifying and fixing the problem in the original source code and recompiling, or by removing the problem code.