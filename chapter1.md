# Guide to implementing the techniques and methods developed in _Highly Comparative Time-Series Analysis_ using an interface between Matlab and a *mySQL* server

## Introduction {#sec:Intro}
This document outlines steps required to set up and implement highly comparative analysis methods on a system using an interface between Matlab and a _mySQL_ database using the highly comparative time-series analysis repository.
The document is an accompaniment this code repository.

A summary of the steps involved in setting up the system is as follows, and will be elaborated on below:

1. Install and set up a mySQL server, setup a new database to store Matlab calculations in, and set up Matlab to be able to communicate with it, [here](#sec:SettingUp).
2. Populate the database with master operations, operations, and time series (using the **install.m** script followed by the user importing their own time series using `SQL_add`), [here](sec:PopulatingDatabase).
3. Run computations to evaluate the operations on all the time series using the **sample_runscript** as a template, [here](sec:calculating).
4. Analyze the results of computations by retrieving calculated data using `TSQ_prepared`, normalizing and filtering it using `TSQ_normalize`, and then making use of a range of analysis and visualization scripts provided, [here](sec:analyzing).

## Setting up {#sec:SettingUp}

This section describes initial tasks that one must perform once, to set
up the <span>*mySQL*</span> database and its interface with Matlab.

### mySQL Database {#sec:mysql_database}

We assume that the user has access to and appropriate read/write privileges for a local or network *mySQL* server database.
Instructions on how to install and set up a *mySQL* database on a variety of operating systems can be found [here][http://dev.mysql.com/doc/refman/5.7/en/installing.html].

### Setting Matlab up to talk to a mySQL server using the java connector {#sec:setting_matlab_up_to_talk_to_mysql}

Before the structure of the database can be created, Matlab must be set
up to be able to talk to the mySQL server. It is necessary to relocate
the J connector from the **Database** directory of this code repository
(which is also freely available [here][http://dev.mysql.com/downloads/connector/j/]): the file
**mysql-connector-java-5.1.27-bin.jar** (for version 5.1.27). Instructions
are here and are summarized below[^1]. This .jar file must be added to a
static path where it can always be found by Matlab. A good candidate
directory is the **java/jarext/** subdirectory of the Matlab root
directory (to determine the Matlab root directory, simply type
`matlabroot` in an open Matlab command window). For Matlab to see this
file, you need to add a reference to it in the **javaclasspath.txt**
file[^2]. This file can be found (or if it does not exist, should be
created) in Matlab’s preferences directory (to determine this location,
type `prefdir` in a command window). This **javaclasspath.txt** file
must contain a text reference to the location of the java connector on
the disk; In the case recommended above, where it has been added to the
**java/jarext** directory, we would add the following to the
javaclasspath.txt file:

```
    $matlabroot/java/jarext/mysql-connector-java-5.1.27-bin.jar
```

ensuring that the version number (5.1.27) matches your version of the J
connector (if you are using a more recent version, for example)[^3].
Note that **javaclasspath.txt** can also be in Matlab’s startup
directory. After restarting Matlab, it should now have the ability to
communicate with mySQL servers (we will check whether this works below).

##Installing the Matlab/mySQL system {#sec:installing_the_matlab_mysql_system}

Many tasks involved in installing the Matlab/mySQL system can be done by
simply running the **install.m** script in the main directory of the
code repository. This script runs the user through the steps outlined
below.

### Creating the mySQL database {#sec:creating_the_mysql_database}

If you have not already done so, creating a mySQL database to use with
Matlab can be done using the `SQL_create_db` function. This requires
that mySQL is installed on an accessible server, or on the local machine
(i.e., using ‘localhost’). If the database has already been set-up, then
you do not need to use the `SQL_create_db` function but you must then
create a text file, **sql-setting.conf**, in the **Database** directory
of the repository. This file contains four comma-delimited entries
corresponding to the server name, database name, username, and password,
as per the following:

    hostname,databasename,username,password

The settings listed here are those used to connect to the mySQL server.
Remember that your password is sitting here in this document in
unencrypted plain text, so don’t use a password that is ever used for
secure purposes.

To check that Matlab can connect to external servers using the mySQL
J-connector, using correct host name, username, and password settings,
we introduce the Matlab routines `SQL_opendatabase` and `SQL_closedatabase`.
An example usage is as follows:

    dbc = SQL_opendatabase; % Opens a connection to the default mySQL database (settings in sql-setting.conf) as `dbc'
            % do things with database using dbc
    SQL_closedatabase(dbc); % Closes the connection labeled `dbc'

For this to work, the **sql_settings.conf** file must be set up
properly. This file specifies (in unencrypted plain text!) the login
details for your mySQL database in the form `hostName,databaseName,username,password`.
An example **sql_settings.conf** file:

    localhost,myTestDatabase,benfulcher,myInsecurePassword

Note that if your database is not set up on your local machine (i.e.,
`localhost`), then Matlab can communicate with a mySQL server through an
ssh tunnel, which requires some [additional setup][sec:sqlssh].
Once you have configured your **sql_settings.conf** file, and you can
run `dbc = SQL_opendatabase;` and `SQL_closedatabase(dbc)` without
errors, then you can smile to yourself and you should at this point be
happy because Matlab can communicate successfully with your mySQL
server! You should also be excited because you are now ready to set up
the database structure.

### Creating the database structure {#sec:DatabaseStructure}

The mySQL database contains four main components:
1. A lists of all the filenames and other metadata of time series (the **TimeSeries** table),
2. A list of all the names and other metadata of pieces of time-series analysis operations (the **Operations** table),
3. A list of all the pieces of code that must be evaluated to give each operation a value, which is necessary, for example, when one piece of code produces multiple outputs (the **MasterOperations** table), and
4. A list of the results of applying operations to time series in the database (the **Results** table). There are some additional subtleties relating to managing keywords, etc., but these basic components form the core of the database.


Time series and operations have their own tables that contain metadata
associated with each piece of data, and each operation, and the results
of applying each method to each time series is in the **Results** table,
that has a row for every combination of time series and operation, where
we also record calculation times and the quality of outputs (for cases
where there the output of the operation was not a real number, or when
some error occurred in the computation). Note that while the time-series
data IS stored on the database, the executable time-series analysis code
files are not such that all code files must be in Matlab’s path (all
paths can be set by running **startup.m**, explained [here][sec:setting_the_path]).

Another handy (but dangerous) function to know about is `SQL_reset`,
which will <span>*delete all data*</span> in the mySQL database, create
all the new tables, and then fill the database with all the time-series
analysis operations.
The **TimeSeries**, **Operations**, and
**MasterOperations** tables can be generated by running the code
`SQL_create_all_tables`, with master operations and operations added to
the database using SQL_add commands (described [here][sec:populating_database]).

You now you have the basic table structure set up in the database and
have done the first bits of mySQL manipulation through the Matlab
interface.

It is very useful to be able to inspect the database directly, using a
graphical interface. A very good example for Mac is the excellent, free
application, [Sequel Pro][http://www.sequelpro.com].
Similar applications exist for Windows and Linux platforms. We encourage the
user to explore the structure of these tables. It is NOT advisable to
manipulate the database directly; instead Matlab scripts should be used
to interact with the database to ensure that the relationships between
the different tables are maintained.

![Visualizing the **Operations** table in the database using
<span>*Sequel Pro*</span> for Mac. Similar applications exist for
Windows and Linux platforms.<span
data-label="fig:SQLProScreenshot"></span>](SQLProScreenshot.png)

### Compiling mex code {#sec:compiling_mex_code}

Many of the operations (especially external code packages) rely on mex
functions, pieces of code not written in Matlab, that need to be
compiled to run natively on each system architecture. To ensure that as
many operations as possible run successfully on your data, you should
compile these mex functions for your system. This requires working
compilers (e.g., gcc, g++) to be installed on your system, which can be
configured using `mex -setup` (cf. `doc mex` for more information). Once
mex is set up, the mex functions used in the time-series code repository
can be compiled by navigating to the **Toolboxes** directory and then
running `` compile`mex ``.

### Compiling the TISEAN binaries {#ssub:compiling_tisean}

Some operations rely on the TISEAN nonlinear time-series analysis
package
(<http://www.mpipks-dresden.mpg.de/~tisean/Tisean_3.0.1/index.html>),
which Matlab accesses via `system` commands, so the TISEAN binaries must
be installed from the commandline. From the **Toolboxes/Tisean_3.0.1**
directory of the repository, try running the following chain of
commands:

    ./configure
    make clean
    make
    make install

This should install the TISEAN binaries in your **$\sim$/bin/**
directory by default (you can instead install into a system-wide
directory (**/usr/bin**, for example) by running **./configure
–prefix=/usr/bin**). You should be able to access these binaries from
the commandline, e.g., typing the command **which poincare** should
return the path to **poincare**. Otherwise, you should check that this
directory is in your path, e.g., by adding

    export PATH=$PATH:$HOME/bin

to your **$\sim$/.bash_profile** (and running **source
$\sim$/.bash_profile**). The path you install TISEAN to will also have
to be in Matlab’s system path, which is added in **startup.m**, and
assumes that the binaries are in **$\sim$/bin**. If you choose to use a
custom location for the TISEAN binaries, that is not in the default
Matlab System Path (**getenv(’PATH’)** in Matlab) then you will have to
add this path. You can test that Matlab can see the TISEAN binaries by
typing, for example:

    !which nstat_z

If Matlab’s system paths are set up correctly, this command should
return the path of your TISEAN binary **nstat_z**.

Populating the database with time series and operations {#sec:populating_database}
=======================================================

Populating the database is done through the function `` SQL`add ``. It
has two inputs that specify: (i) Whether to import a set of time series
(specify ‘ts’), a set of operations (‘ops’), or a set of master
operations (‘mops’), (ii) The name of the input text file that contains
appropriately-formatted information about the time series, master
operations, or operations. Note that manually adding or deleting rows to
the database can create inconsistencies and errors in the database
structure and should not be done unless you really know what you’re
doing—i.e., adding time series and operations to the database should
always be done using `` SQL`add `` which sets up the Results table of
the database and ensures that the indexing relationships in the database
are properly maintained. We deal with adding master operations
(Sec. [sec:adding~m~ops]), operations (Sec. [sec:adding~o~ps]), and time
series (Sec. [sec:adding~t~ime~s~eries]) separately. Users wishing to
run the existing library of code on their own set of time series will
only need to add time series (Sec. [sec:adding~t~ime~s~eries]), as the
full operation library is added in the install script.

Adding master operations to the database {#sec:adding_mops}
----------------------------------------

By default, during the install process of this software, our library of
time-series analysis code populates the database. The formatted input
file specifying these pieces of code and their input parameters is
**INP_mops.txt** in the **Database** directory of the repository. These
operations can be added to the database using the following command:

    SQL_add('mops','INP_mops.txt');

Two example lines from the input file, ‘INP_mops.txt’, are as follows:

    CO_tc3(y,1)     CO_tc3_y_1
    ST_length(x)    ST_length

Each line in the input file specifies a piece of code and its input
parameters as well as a unique name for that master operation, separated
by whitespace; this name is referenced by individual operations. Note
that <span>*x*</span> refers to the input time series and
<span>*y*</span> refers to the $z$-scored input time series. In the
example above, the first line thus adds an entry in the database for
running the code `` CO`tc3 `` using a <span>*z*</span>-scored time
series as input, with ‘1’ as the second input with the label
<span>*CO_tc3_y_1*</span>, and the second line will add an entry for
running the code `` ST`length `` on a time series input, using the label
<span>*length*</span>. The output of all master operations should either
be a real number, a structure, or a ‘NaN’ to indicate that the input
time series is not appropriate for this code. It is the operations that
make up elements of the data matrix.

All pieces of code should be accessible in the current Matlab path. When
the time comes to perform computations on data using the methods in the
database, we assume that Matlab has access to each of the functions
specified by their code name in the database. For the above example,
this means that we assume Matlab has access to the function `` CO`tc3 ``
(because it will try to run `` CO`tc3(y,1) ``), and also the function
`` ST`length(x) ``.

Adding operations to the database {#sec:adding_ops}
---------------------------------

Operations can be added to the mySQL database using the following:

    SQL_add('ops','INP_ops.txt');

The input file, e.g., **INP_ops.txt** (in the **Database** directory)
should contain a row for every operation, and use labels that correspond
to master operations. An example excerpt from such a file is below:

    CO_tc3_y_1.raw     CO_tc3_1_raw      correlation,nonlinear
    CO_tc3_y_1.abs     CO_tc3_1_abs      correlation,nonlinear
    CO_tc3_y_1.num     CO_tc3_1_num      correlation,nonlinear
    CO_tc3_y_1.absnum    CO_tc3_1_absnum     correlation,nonlinear
    CO_tc3_y_1.denom     CO_tc3_1_denom    correlation,nonlinear
    ST_length                  length                        raw,LengthDependent

The first column references a corresponding master label and, in the
case of master operations that produce structure, the particular field
of the structure to reference (after the fullstop), the second column
denotes the label for the operation, and the final column is a set of
comma-delimited keywords (that must not include whitespace). White space
is used to separate the three entries on each line of the input file. In
this example, the master operation labeled **CO_tc3_y_1**, outputs is
a structure, with fields that are referenced by the first five
operations listed here, and the **ST_length** master operation outputs
a single number (the length of the time series), which is referenced by
the operation named ‘length’ here. The two keywords ‘correlation’ and
‘nonlinear’ are added to the `` CO`tc3 `` operations, while the keywords
‘raw’ and ‘LengthDependent’ are added to the `` ST`length `` operation.
These keywords can be used to organize and filter the set of operations
used for a given analysis task.

Every operation added to the database will be given a unique integer
identifier, **op_id**, which provides a common way of retrieving
specific operations from the database. Similarly, every master operation
is given a unique integer, **mop_id**, that can be used to identify it.

Note that after (hopefully successfully) adding the operations to the
database, the `` SQL`add `` function indexes the operation keywords to
an **OperationKeywords** table that produces a unique identifier for
each keyword, and another linking table that allows operations with each
keyword to be retrieved efficiently.

Adding time series to the database {#sec:adding_time_series}
----------------------------------

The most common task is adding time series to the database, which must
be done for each dataset analysed. It is up to personal preference of
the user whether to keep all time-series data in a single database, or
have a different database for each dataset. Adding a set of time series
to the database is done as above, and for a given input file with
filename **INP_ts.txt**, for example, the appropriate code is

    SQL_add('ts','INP_ts.txt');

We provide an example input file in the **Database** directory as
**INP_test_ts.txt**. The **SQL_add** function expects the text file
to be formatted with each row specifying the filename of a time series
and comma-delimited keywords with whitespace between them. For example,
the following input file would add two time series named
‘gaussianwhitenoise_001.dat’ and ‘gaussianwhitenoise_002.dat’ to the
database, each with keywords ‘noise’ and ‘gaussian’, and it would then
add the time series stored in the file ‘sinusoid_001.dat’ with keywords
‘periodic’ and ‘sine’:

    gaussianwhitenoise_001.dat     noise,gaussian
    gaussianwhitenoise_002.dat     noise,gaussian
    sinusoid_001.dat               periodic,sine

Every time series added to the database will be given a unique integer
identifier, `` ts`id ``, which is used to retrieve specific time series
from the database. `` SQL`add `` will attempt to find the time-series
data files given in the input file, read them (using `dlmread`), and
then import all of this data into the database. Once imported, the
time-series data is stored in the database, and the original files in
the input file no longer need to kept in the Matlab path. If keywords
are provided in the input file, the time series are indexed and then
updated in the **TimeSeriesKeywords** table and associated index table.

Calculating {#sec:calculating}
===========

In this section we describe our Matlab framework for computing the
output of operations in the **Operations** table of the
<span>*mySQL*</span> database on time series in the **TimeSeries** table
of the database. The main procedure involves three steps:

1.  Retrieve a set of time series and operations from (the **Results**
    table) of the database to a local Matlab file, **HCTSA_loc**
    [`` TSQ`prepared ``].

2.  Compute the operations on the retrieved time series in Matlab and
    storing the results locally [`TSQ_brawn`].

3.  Write the results back to the **Results** table of the database
    [`TSQ_agglomerate`].

The process is represented schematically in
Fig. [fig:ComputationSchematic]. A sample script to iterate over these
steps is the `` sample`runscript `` in the **Calculation** directory of
the repository. Additional details of the steps involved are below.

![ **Computation workflow schematic.** The three steps involved in
computing time-series analysis operations on a set of time series are
labeled: **1**. `` TSQ`prepared `` (retrieve data, including missing
entries, from the database), **2**. `` TSQ`brawn `` (compute missing
time series/operation pairs in HCTSA_loc.mat), **3**.
`` TSQ`agglomerate `` (store the new results back in the database).
<span
data-label="fig:ComputationSchematic"></span>](ComputationSchematic.pdf)

Retrieving data from the database using TSQ_prepared {#sec:tsq_prepared}
-----------------------------------------------------

Retrieving data from the **Results** table of the database is typically
done for one of two purposes: (i) To calculate as-yet uncalculated
entries to be stored back into the database, and (ii) To analyze
already-computed data stored in the database in Matlab. The function
`` TSQ`prepared `` performs both of these functions, using different
inputs. Here we describe the use of the `` TSQ`prepared `` function for
the purposes of populating uncalculated (NULL) entries in the
**Results** table of the database in Matlab.

For calculating missing entries in the database, `` TSQ`prepared `` can
be run as follows:

    TSQ_prepared(ts_ids, op_ids, 'null');

The third input, <span>*‘null’*</span>, retrieves ts_ids and op_ids
from the sets provided that contain (as-yet) uncalculated (i.e., NULL)
elements in the database; these can then be calculated and stored back
in the database. An example usage is given below:

    TSQ_prepared([1,3], 1:500, 'null');

Running this code will retrieve data for time series with `` ts`id ``s 1
and 3 and operations with `` op`id `` in the range 1 to 500, keeping
only the rows and columns of the resulting time series $\times$
operations matrix that contain NULLs. When calculations are complete and
one wishes to analyze all of the data stored in the database (not just
NULL entries requiring computation), the third input should be set to
‘all’ to retrieve all entries in the **Results** table of the database.

The result of running `` TSQ`prepared `` is a local Matlab file,
**HCTSA_loc.mat**, that contains the relevant data retrieved from the
database. The file contains the following elements:

-   **TS_DataMat** is a $n \times m$ matrix corresponding to the $n$
    time series and $m$ operations retrieved from the database. Elements
    of **TS_DataMat** correspond to the result of applying each
    operation to each time series.

-   **TS_Quality** is an $n \times m$ matrix containing quality labels
    for each operation output. Quality labels are shown in
    Tab. [tab:quality~l~abels].

-   **TS_CalcTimes** is an $n \times m$ matrix containing calculation
    times for each operation output. Note that for operations that point
    to a structure produced by a master operation operations, the
    calculation time stored is that taken to compute the entire master
    function from which they were derived.

-   **TimeSeries** is a structure array containing metadata about the
    time series retrieved (corresponding to rows of the **TS_**
    matrices).

-   **Operations** is a structure array containing metadata about the
    operations retrieved (columns of the **TS_** matrices).

-   **MasterOperations** is a structure array containing metadata about
    the master operations retrieved, corresponding to the code evaluated
    that is referenced by each operation.

Note that NULL entries in the database are converted to NaN entries in
the local Matlab matrices.

   <span>**Quality label**</span>                <span>**Description**</span>
  -------------------------------- ---------------------------------------------------------
                 0                  No problems with calculation. Output was a real number.
                 1                   When running the code, a fatal error was encountered.
                 2                       Output from the code was <span>*NaN*</span>.
                 3                              Output was <span>*Inf*</span>.
                 4                              Output was <span>*-Inf*</span>.
                 5                         Output had a nonzero imaginary component.

  :  **Quality labels**. These are stored in the **Quality** column of
  the **Results** table in the <span>*mySQL*</span> database (and
  locally in **TS_Quality** matrix). Values are used to indicate
  non-real-valued outputs from operations, or cases when fatal errors
  were encountered. Note that when the quality label is nonzero (i.e.,
  the quality encodes a special-valued output), the actual output value
  of the operation is set to zero, as a convention.<span
  data-label="tab:qualitylabels"></span>

Performing calculations using TSQ_brawn {#sec:performing_calculations}
----------------------------------------

Once a relevant section of the data matrix has been retrieved, we can
calculate values that have not previously been calculated, indicated by
NULL entries in the **Results** table of the database, and NaN entries
in the corresponding **HCTSA_loc.mat**.

Calculations are performed using the function `TSQ_brawn`, which stores
results back into the matrices in **HCTSA_loc**. This function is
generally run without inputs:

        TSQ_brawn;

Inputs to the function are optional and can be used to specify whether
to log to file or the screen (‘tolog’, default: no), and whether to
compute operations across cores using the Parallel processing
(‘toparallel’, default: no). Running `TSQ_brawn` will begin running
operations on time series in **HCTSA_loc.mat** for which elements in
**TS_DataMat** are NaNs (indicating that they have not been run
before), and storing the results back in the matrices of
**HCTSA_loc.mat**, i.e., **TS_DataMat** (output of each operation on
each time series), **TS_CalcTime** (calculation time for each operation
on each time series), and **TS_Quality** (labels indicating errors or
special-valued outputs). When all NULL entries in **TS_DataMat** have
been calculated, **TSQ_brawn** saves the results back to the local
file: **HCTSA_loc.mat**. These results can then be written back to the
database using `TSQ_agglomerate`, as shown in
Fig. [fig:ComputationSchematic].

Writing calculations back to the database using TSQ_agglomerate {#sec:writing_calculations_back_to_the_database}
----------------------------------------------------------------

Once calculations have been performed using Matlab on local files, the
results must be written back to the database. This task is performed by
`TSQ_agglomerate`, which reads the data in **HCTSA_loc.mat**, checks
that the metadata still matches the database, and then begins updating
the **Output**, **Quality**, and **CalculationTime** columns of the
**Results** table in the <span>*mySQL*</span> database. This can be done
by simply running:

        TSQ_agglomerate;

Cycling through calculations using runscripts {#sec:cycling_through_calculations_using_runscripts}
---------------------------------------------

This section on computation has outlined the three main steps to
computation as (i) retrieving from the database (`TSQ_prepared`), (ii)
calculating operations on time series (`TSQ_brawn`), and (iii) writing
the results back to the database (`TSQ_agglomerate`). In order to
calculate across large sections of the database, it is convenient to use
runscripts, that allow large computations to be broken up into smaller
pieces, and using `for` loops to cycle through these pieces. By
designating different sections of the database to cycle through, this
procedure can also be used to (manually) distribute the computation
across different machines. Retrieving a large section of the database at
once can be problematic because it requires large disk reads and writes,
uses a lot of memory, and if problems occur in the reading or writing
to/from files, one may have to abandon a large number of existing
computations. Due to the ability of `TSQ_brawn` to parallelize the
computation across multiple CPU processors, it is usually the most
efficient practice to retrieve a small number of time series at each
iteration of the prepared–brawn–agglomerate loop. Depending on their
length, we usually retrieve five time series at each iteration of a
runscript loop. An example runscript is given in the code that
accompanies this document, as `sample_runscript.m`, and can be viewed as
a template for runscripts that one may wish to use when performing
time-series calculations across the database.

Dealing with errors {#sec:dealing_with_errors}
-------------------

Errors in particular pieces of code can often occur when applying them
to large numbers of time series. Some errors are not problems with the
code, but problems with applying particular sets of code to particular
time series, such as when a Matlab fitting function reaches the maximum
number of iterations and returns an error. These are kept and stored as
errors. Other errors are genuine problems with the code that need to be
corrected. Although we have attempted to preempt and deal with as many
errors in the code as possible, ongoing feedback will be used to further
refine and develop the code over time. Note that errors returned from
Matlab files are dealt with using `try-catch` statements, but errors
with <span>*.mex*</span> functions can produce a fault that crashes
Matlab or the system. These situations must be dealt with by either
identifying and fixing the problem in the original source code and
recompiling, or by skipping the problem operation (or a problem time
series).

Performing highly comparative analysis {#sec:analyzing}
======================================

Once the database has been set up, populated with data and operations,
and the operations have been computed on the time-series data, the
result can be used to perform a wide variety of highly comparative
analyses, such as those outlined in @Fulcher13.

The types of methods employed on a given dataset should be developed to
suit specific time-series analysis tasks. Setting up the problem,
guiding the methodology, and interpreting the results requires strong
scientific input that should draw on domain knowledge, the questions
asked of the data, experience performing data analysis, and statistical
considerations. Once a highly comparative dataset is produced, users can
be creative in their exploration and analysis of the data, or draw upon
a library of analytic techniques that we have developed.

The first step of any analysis is to retrieve a relevant portion of data
from the database to local Matlab files for analysis. As described
above, this is done using the `TSQ_prepared` function. Example usage is
provided:

    TSQ_prepared(ts_ids, m_ids,'all');

for vectors **ts_ids** and **op_ids** specifying the ts_ids and
op_ids to be retrieved from the database. Running the code in this way,
using the ‘all’ tag, ensures that the full range of ts\_ids and op\_ids
specified are retrieved from the database and stored in the local file,
**HCTSA_loc.mat**, which can then form the basis of subsequent
analysis.

## Filtering and normalizing the data using TSQ_normalize {#sec:normalization}

The first step in analyzing a dataset involves processing the data
matrix. This involves filtering out operations or time series that
produced many errors or special-valued outputs, and then normalizing of
the output of all operations, which is typically done in-sample,
according to an outlier-robust sigmoidal transform (although other
normalizing transformations can be selected). Both of these tasks are
performed using the function `TSQ_normalize`. Example usage is as
follows:

    TSQ_normalize(`scaledSQzscore',[0.8,1.0]);

The first input controls the normalization method, in this case a scaled
outlier-robust sigmoidal transformation, and the second input controls
the filtering, in this case each time series needs to produce at least
80% good-valued outputs (setting 0.8), or they are removed, and then
operations with less than 100% good-valued outputs are removed (setting
1.0). These can be relaxed, to, for example, [0.7,0.9], which removes
time series with less than 70% good values, and then removes operations
with less than 90% good values. When neither value is 1.0, this can
leave NaN values in the resulting data matrix, which can affect some
calculations that cannot deal with missing values (such as PCA). Some
applications can tolerate some special-valued outputs from operations
(like some clustering methods, where distances are simply calculated
using those operations that are did not produce special-valued outputs
for each pair of objects), but others cannot (like Principal Components
Analysis); the filtering parameters should be specified accordingly.
Similarly, it makes sense to weight each operation equally for the
purposes dimensionality reduction, and thus normalize all operations to
the same range using a transformation like ‘scaledSQzscore’,
‘scaledSigmoid’, ‘mixedSigmoid’. For the case of calculating mutual
information distances between operations, however, one would rather not
distort the distributions and perform no normalization, using ‘raw’ or a
linear transformation like ‘zscore’, for example. The list of
implemented normalization transformations can be found in the function
`BF_NormalizeMatrix`. An example usage:

        TSQ_normalize('scaledSQzscore',[0.8,0.8]);

This filters time series (rows of the data matrix) with more than 20%
special-values, then filters out operations (columns of the data matrix)
with more than 20% special values, then applies the outlier-robust
‘scaledSQzscore’ sigmoidal transformation to all remaining operations
(columns). Note that this transformation does not tolerate distributions
with an interquartile range of zero, which will be filtered out.

Another example:

        TSQ_normalize('raw',[0.8,1]);

This filters time series (rows of the data matrix) with more than 20%
special-values, then filters out operations (columns of the data matrix)
with any special values whatsoever, leaving a data matrix with
well-behaved operations as columns and containing no special or missing
values. No normalizing transformation is applied to the remaining
operations.

The `TSQ_normalize` function writes the new, filtered, normalized matrix
to a local file called **HCTSA_N**. This contains normalized, and
trimmed versions of the information in **HCTSA_loc.mat**. Analysis can
now be performed on the data contained in **HCTSA_N**, in the knowledge
that different settings for filtering and normalizing the results can be
applied at any time by simply rerunning `TSQ_normalize`, which will
overwrite the existing **HCTSA_N.mat** with the results of the new
normalization and filtration settings.

Clustering the data matrix using TSQ_cluster {#sec:clustering}
---------------------------------------------

For the purposes of visualizing the data matrix, it is often desirable
to have the rows and columns reordered to put similar rows adjacent to
one another, and similarly to place similar columns adjacent to one
another. This reordering can be done by the function `TSQ_cluster`,
which has five inputs:

        TSQ_cluster(ClusterMethRow, ClusterParamsRow, ClusterMethCol, ClusterParamsCol, SubSet);

The inputs are as follows:

-   *ClusterMethRow*: a string that sets how to cluster the
    rows. The usual choice is linkage clustering, which is set using
    ‘linkage’. Can set to ‘none’ for no clustering of the rows.

-   *ClusterParamsRow*: a cell that defines parameters of
    the clustering method specified as *ClusterMethRow*. A
    typical choice for linkage clustering is
    {‘euclidean’,‘average’,0,[],0}, which uses euclidean distances and
    average linkage clustering.

-   *ClusterMethCol*: a string that sets how to cluster the
    columns. The most typical choice is ‘linkage’, but can set to ‘none’
    for no column clustering.

-   *ClusterParamsCol*: a cell defining parameters of the
    clustering method specified as *ClusterMethCol*. An
    typical choice for linkage clustering is {‘corr’,‘average’,0,[],0},
    which uses linear correlation distances between operations and
    average linkage clustering.

-   *SubSet*: Allows one to cluster only a specified subset
    of the columns and rows in **HCTSA_N**. An example usage is
    {‘norm’,[1,5,6,7],[]}, which would keep only row indices 1,5,6, and
    7, and all columns from **HCTSA_N**.

Note that `TSQ_cluster` uses the mechanics of a more general
unsupervised clustering function **TSQ_us_cluster**, where more
information about choices for clustering algorithms (for
*ClusterMethRow* and *ClusterMethCol*) and the
cells of parameters (for *ClusterParamsRow* and
*ClusterParamsCol*) can be found. Note that the output of
this function is to local Matlab files; in the same way that
`TSQ_normalize` takes in **HCTSA_loc** and outputs normalized versions
as **HCTSA_N**, `TSQ_cluster` takes in **HCTSA_N** and writes output
to a new file: **HCTSA_cl**, containing clustered (re-ordered) versions
of the data in **HCTSA_N**.

### Grouping elements {#sec:grouping_variables}

Throughout our analysis, it is often important to incorporate grouped
structure particularly between time series in a classification dataset,
for example. This is done using `TSQ_LabelGroups`.

# Visualizing {#sec:visualizing}

There are many tasks that involve understanding the rich structure
contained in data matrices by visualizing them. In this section we
describe some basic tools we have developed to visualize the behavior of
time series and operations in the data matrix.

Visualizing the data matrix using TSQ_plot_DataMatrix {#sec:visualizing_the_data_matrix}
-------------------------------------------------------

    TSQ_plot_DataMatrix

[[mention how keywords can be used using the `TSQ_LabelGroups`]]
[[should include the data matrix visualization code, dimensionality
reduction code, and greedy feature selection code, as examples, each
with example workflows starting from retrieval from the database]]
[[mention how do similarity search, etc.]]

Additional details {#sec:little_details}
==================

Setting the path {#sec:setting_the_path}
----------------

A useful way to keeping track with the directories you need to add to
Matlab’s path involves creating a .m file containing `addpath` commands
that can be run at the start of each Matlab session. We used this to
keep track of the code for operations, for analysis, third-party
toolboxes, and time-series data files, which could be easily altered at
any point. An example is given in the code accompanying this document,
as `startup.m`.

Setting up an ssh tunnel to a mySQL server {#sec:sqlssh}
------------------------------------------

In some cases, the mySQL server you wish to connect to requires an ssh
tunnel. One solution is to use port forwarding from your local machine
to the server. The port forward can be set up in the terminal using a
command like:

        ssh -L 1234:localhost:3306 myUsername@myServer.edu

This command connects port 1234 on your local computer to port 3306 (the
default mySQL port) on the server. Now, telling Matlab to connect to
`localhost` through port 1234 will connect it, through the established
ssh tunnel, to the server. This can be achieved by specifying the server
as `localhost` and the port number as 1234 in the **sql_settings.conf**
file (or during the `install` process).

Maintaining the database {#sec:maintaining_the_database}
------------------------

In this section we describe how keywords and other types of metadata
stored in the database can be manipulated, and how results of whole sets
of operations and time series can be cleared or completely deleted from
the database. These tasks are implemented as Matlab functions, and
ensure that key database structures are maintained. Instead performing
such tasks by acting directly on the database can cause inconsistencies
and should be avoided.

Error handling {#sec:error_handling}
==============

Java heap space
---------------

Running out of java heap space throws the error
**java.lang.OutOfMemoryError**, and usually happens when trying to
retrieve a large set of time series/operations from the database. Matlab
needs to keep the whole retrieval in memory, and has a hard limit on
this. The java heap size can be increased in the Matlab preferences,
under <span>*General*</span> $\rightarrow$ <span>*Java Heap
Memory*</span>.

[^1]: cf.
    <http://www.mathworks.co.uk/help/matlab/matlab_external/bringing-java-classes-and-methods-into-matlab-workspace.html>

[^2]: An alternative is to modify the **classpath.txt** file directly,
    but this may not be supported by newer versions of Matlab.

[^3]: As mentioned above, this can be achieved by editing the
    **classpath.txt** file using `edit classpath.txt` in an open Matlab
    command window and then adding the line above to it, corresponding
    to the location of the j-connector on disk.
