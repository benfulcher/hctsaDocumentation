## Installing the *hctsa* code package

The *hctsa* package can usually be set up by running the `install` script, which installs and sets up a *mySQL* server and database, populates the database with operations, and then compiles all of the mex functions required by Matlab to run all of the operations.

A summary of the steps involved in setting up the system is as follows, and will be elaborated on below:

1. Install and set up a *mySQL* server, setup a new database to store Matlab calculations in, and set up Matlab to be able to communicate with it, [here](#sec:SettingUp).
2. Populate the database with master operations, operations, and time series (using the `install` script followed by the user importing their own time series using `SQL_add`), [here](sec:PopulatingDatabase).
3. Run computations to evaluate the operations on all the time series using the `sample_runscript` as a template, [here](sec:calculating).
4. Analyze the results of computations by retrieving calculated data using `TSQ_prepared`, normalizing and filtering it using `TSQ_normalize`, and then making use of a range of analysis and visualization scripts provided, [here](sec:analyzing).

## Setting up {#sec:SettingUp}

This section describes initial tasks that one must perform once, to set up the *mySQL* database and its interface with Matlab.

### mySQL Database {#sec:mysql_database}

We assume that the user has access to and appropriate read/write privileges for a local or network *mySQL* server database.
Instructions on how to install and set up a *mySQL* database on a variety of operating systems can be found [here](http://dev.mysql.com/doc/refman/5.7/en/installing.html).

### Setting Matlab up to talk to a mySQL server using the java connector {#sec:SettingUpJ}

Before the structure of the database can be created, Matlab must be set
up to be able to talk to the mySQL server. It is necessary to relocate the J connector from the **Database** directory of this code repository (which is also freely available [here](http://dev.mysql.com/downloads/connector/j/)): the file `mysql-connector-java-5.1.27-bin.jar` (for version 5.1.27).
Instructions are here and are summarized below, and described in the [Matlab documentation](http://www.mathworks.co.uk/help/matlab/matlab_external/bringing-java-classes-and-methods-into-matlab-workspace.html).
This .jar file must be added to a static path where it can always be found by Matlab.
A good candidate directory is the **java/jarext/** subdirectory of the Matlab root directory (to determine the Matlab root directory, simply type `matlabroot` in an open Matlab command window).
For Matlab to see this file, you need to add a reference to it in the `javaclasspath.txt` file (An alternative is to modify the **classpath.txt** file directly, but this may not be supported by newer versions of Matlab).
This file can be found (or if it does not exist, should be created) in Matlab’s preferences directory (to determine this location, type `prefdir` in a command window).
This `javaclasspath.txt` file must contain a text reference to the location of the java connector on the disk; In the case recommended above, where it has been added to the **java/jarext** directory, we would add the following to the `javaclasspath.txt` file:

    $matlabroot/java/jarext/mysql-connector-java-5.1.27-bin.jar

ensuring that the version number (5.1.27) matches your version of the J connector (if you are using a more recent version, for example).
As mentioned above, this can be achieved by editing the **classpath.txt** file using `edit classpath.txt` in an open Matlab command window and then adding the line above to it, corresponding to the location of the j-connector on disk.
Note that **javaclasspath.txt** can also be in Matlab’s startup directory.
After restarting Matlab, it should now have the ability to communicate with *mySQL* servers (we will check whether this works below).

## Installing the Matlab/mySQL system {#sec:installing_the_matlab_mysql_system}

Many tasks involved in installing the Matlab/mySQL system can be done by simply running the `install.m` script in the main directory of the code repository.
This script runs the user through the steps outlined below.

### Creating the mySQL database {#sec:creating_the_mysql_database}

If you have not already done so, creating a mySQL database to use with Matlab can be done using the `SQL_create_db` function.
This requires that mySQL is installed on an accessible server, or on the local machine (i.e., using `localhost`).
If the database has already been set up, then you do not need to use the `SQL_create_db` function but you must then create a text file, `sql-setting.conf`, in the **Database** directory of the repository.
This file contains four comma-delimited entries corresponding to the server name, database name, username, and password, as per the following:

    hostname,databasename,username,password

The settings listed here are those used to connect to the mySQL server.
Remember that your password is sitting here in this document in unencrypted plain text, so do not use a secure or important password.

To check that Matlab can connect to external servers using the mySQL J-connector, using correct host name, username, and password settings, we introduce the Matlab routines `SQL_opendatabase` and `SQL_closedatabase`.
An example usage is as follows:

```matlab
    dbc = SQL_opendatabase; % Opens a connection to the default mySQL database
    % (settings in sql-setting.conf) as `dbc'
    % do things with database using dbc
    SQL_closedatabase(dbc); % Closes the connection labeled dbc
```

For this to work, the **sql_settings.conf** file must be set up properly.
This file specifies (in unencrypted plain text!) the login details for your mySQL database in the form `hostName,databaseName,username,password`.
An example **sql_settings.conf** file:

    localhost,myTestDatabase,benfulcher,myInsecurePassword

Note that if your database is not set up on your local machine (i.e., `localhost`), then Matlab can communicate with a mySQL server through an ssh tunnel, which requires some [additional setup](#sec:sqlssh).
Once you have configured your **sql_settings.conf** file, and you can run `dbc = SQL_opendatabase;` and `SQL_closedatabase(dbc)` without errors, then you can smile to yourself and you should at this point be happy because Matlab can communicate successfully with your mySQL server!
You should also be excited because you are now ready to set up the database structure.

### Creating the database structure {#sec:DatabaseStructure}

The mySQL database contains four main components:

1. A lists of all the filenames and other metadata of time series (the **TimeSeries** table),
2. A list of all the names and other metadata of pieces of time-series analysis operations (the **Operations** table),
3. A list of all the pieces of code that must be evaluated to give each operation a value, which is necessary, for example, when one piece of code produces multiple outputs (the **MasterOperations** table), and
4. A list of the results of applying operations to time series in the database (the **Results** table). There are some additional subtleties relating to managing keywords, etc., but these basic components form the core of the database.

Time series and operations have their own tables that contain metadata associated with each piece of data, and each operation, and the results of applying each method to each time series is in the **Results** table, that has a row for every combination of time series and operation, where we also record calculation times and the quality of outputs (for cases where there the output of the operation was not a real number, or when some error occurred in the computation).
Note that while the time-series data *is* stored on the database, the executable time-series analysis code
files are not such that all code files must be in Matlab’s path (all paths can be set by running **startup.m**, explained [here](#sec:SettingThePath).

Another handy (but dangerous) function to know about is `SQL_reset`, which will **delete all data** in the mySQL database, create all the new tables, and then fill the database with all the time-series analysis operations.
The **TimeSeries**, **Operations**, and **MasterOperations** tables can be generated by running the code `SQL_create_all_tables`, with master operations and operations added to the database using SQL_add commands (described [here](sec:populating_database)).

You now you have the basic table structure set up in the database and have done the first bits of mySQL manipulation through the Matlab interface.

It is very useful to be able to inspect the database directly, using a graphical interface.
A very good example for Mac is the excellent, free application, [Sequel Pro](http://www.sequelpro.com).
Similar applications exist for Windows and Linux platforms.
We encourage the user to explore the structure of these tables.
It is NOT advisable to manipulate the database directly; instead Matlab scripts should be used to interact with the database to ensure that the relationships between the different tables are maintained.

![SQLPro for Mac](SQLProScreenshot.png)
<!--Visualizing the **Operations** table in the database using *Sequel Pro* for Mac. Similar applications exist for Windows and Linux platforms. -->

### Compiling mex code {#sec:CompilingMexCode}

Many of the operations (especially external code packages) rely on mex functions, pieces of code not written in Matlab, that need to be compiled to run natively on each system architecture.
To ensure that as many operations as possible run successfully on your data, you should compile these mex functions for your system.
This requires working compilers (e.g., gcc, g++) to be installed on your system, which can be configured using `mex -setup` (cf. `doc mex` for more information).
Once mex is set up, the mex functions used in the time-series code repository can be compiled by navigating to the **Toolboxes** directory and then running `compile_mex`.

### Compiling the TISEAN binaries {#sec:CompilingTisean}

Some operations rely on the [TISEAN nonlinear time-series analysis package](http://www.mpipks-dresden.mpg.de/~tisean/Tisean_3.0.1/index.html), which Matlab accesses via `system` commands, so the TISEAN binaries must be installed from the commandline.
From the **Toolboxes/Tisean_3.0.1** directory of the repository, run the following chain of commands:

    ./configure
    make clean
    make
    make install

This should install the TISEAN binaries in your **~/bin/** directory by default (you can instead install into a system-wide directory (**/usr/bin**, for example) by running `./configure –prefix=/usr/bin`). You should be able to access these binaries from the commandline, e.g., typing the command `which poincare` should return the path to the TISEAN function `poincare`.
Otherwise, you should check that this directory is in your path, e.g., by adding

    export PATH=$PATH:$HOME/bin

to your **~/.bash_profile** (and running `source ~/.bash_profile`).
The path you install TISEAN to will also have to be in Matlab’s system path, which is added in **startup.m**, and assumes that the binaries are in **~/bin**.
If you choose to use a custom location for the TISEAN binaries, that is not in the default Matlab system path (`getenv(``PATH')` in Matlab) then you will have to add this path.
You can test that Matlab can see the TISEAN binaries by typing, for example, the following into Matlab:

    !which nstat_z

If Matlab’s system paths are set up correctly, this command should return the path of your TISEAN binary `nstat_z`.

### Setting the path
<!-- {#sec:settingPath} -->

A useful way to keeping track with the directories you need to add to Matlab’s path involves creating a `.m` file containing `addpath` commands that can be run at the start of each Matlab session.
We used this to keep track of the code for operations, for analysis, third-party toolboxes, and time-series data files, which could be easily altered at any point.
An example is given in the code accompanying this document, as `startup.m`.

### Setting up an ssh tunnel to a mySQL server
<!-- {#sec:sqlssh} -->

In some cases, the mySQL server you wish to connect to requires an ssh tunnel.
One solution is to use port forwarding from your local machine to the server.
The port forward can be set up in the terminal using a command like:

    ssh -L 1234:localhost:3306 myUsername@myServer.edu

This command connects port 1234 on your local computer to port 3306 (the default mySQL port) on the server.
Now, telling Matlab to connect to `localhost` through port 1234 will connect it, through the established ssh tunnel, to the server.
This can be achieved by specifying the server as `localhost` and the port number as 1234 in the `sql_settings.conf` file (or during the `install` process).

### Maintaining the database
<!-- {#sec:maintainingDatabase} -->

In this section we describe how keywords and other types of metadata stored in the database can be manipulated, and how results of whole sets of operations and time series can be cleared or completely deleted from the database.
These tasks are implemented as Matlab functions, and ensure that key database structures are maintained. Instead performing such tasks by acting directly on the database can cause inconsistencies and should be avoided.

## Error handling {#sec:error_handling}

### Java heap space

Running out of java heap space throws the error `java.lang.OutOfMemoryError`, and usually happens when trying to retrieve a large set of time series/operations from the database.
Matlab needs to keep the whole retrieval in memory, and has a hard limit on this.
The java heap size can be increased in the Matlab preferences, under *General* $$\rightarrow$$ *Java Heap
Memory*.