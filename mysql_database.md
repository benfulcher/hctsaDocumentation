# Setting up the mySQL database

We assume that the user has access to and appropriate read/write privileges for a local or network *mySQL* server database.
Instructions on how to install and set up a *mySQL* database on a variety of operating systems can be found [here](http://dev.mysql.com/doc/refman/5.7/en/installing.html).

## Setting Matlab up to talk to a mySQL server using the java connector
<!--{#sec:SettingUpJ}-->

Before the structure of the database can be created, Matlab must be set up to be able to talk to the mySQL server.
It is necessary to relocate the J connector from the **Database** directory of this code repository (which is also freely available [here](http://dev.mysql.com/downloads/connector/j/)): the file `mysql-connector-java-5.1.27-bin.jar` (for version 5.1.27).
Instructions are here and are summarized below, and described in the [Matlab documentation](http://www.mathworks.co.uk/help/matlab/matlab_external/bringing-java-classes-and-methods-into-matlab-workspace.html).
This .jar file must be added to a static path where it can always be found by Matlab.
A good candidate directory is the **java/jarext/** subdirectory of the Matlab root directory (to determine the Matlab root directory, simply type `matlabroot` in an open Matlab command window).

For Matlab to see this file, you need to add a reference to it in the `javaclasspath.txt` file (An alternative is to modify the **classpath.txt** file directly, but this may not be supported by newer versions of Matlab).
This file can be found (or if it does not exist, should be created) in Matlab’s preferences directory (to determine this location, type `prefdir` in a command window).
This `javaclasspath.txt` file must contain a text reference to the location of the java connector on the disk; In the case recommended above, where it has been added to the **java/jarext** directory, we would add the following to the `javaclasspath.txt` file:

```matlab
    $matlabroot/java/jarext/mysql-connector-java-5.1.27-bin.jar
```

ensuring that the version number (5.1.27) matches your version of the J connector (if you are using a more recent version, for example).

As mentioned above, this can be achieved by editing the **classpath.txt** file using `edit classpath.txt` in an open Matlab command window and then adding the line above to it, corresponding to the location of the j-connector on disk.
Note that **javaclasspath.txt** can also be in Matlab’s startup directory.
After restarting Matlab, it should now have the ability to communicate with *mySQL* servers (we will check whether this works below).

## Installing the Matlab/mySQL system
<!--{#sec:installing_the_matlab_mysql_system}-->

The main tasks involved in installing the Matlab/mySQL interface are achieved by the `install.m` script, which runs the user through the steps below.
<!--in the main directory of the code repository.-->
<!--This script runs the user through the steps outlined below.-->


### Creating the mySQL database
<!--{#sec:creating_the_mysql_database}-->

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
    % Open a connection to the default mySQL database as dbc:
    % Connection details are stored in sql-setting.conf
    dbc = SQL_opendatabase;
    
    % <Do things with the database connection, dbc>
    
    % Close the connection, dbc:
    SQL_closedatabase(dbc);
```

For this to work, the **sql_settings.conf** file must be set up properly.
This file specifies (in unencrypted plain text!) the login details for your mySQL database in the form `hostName,databaseName,username,password`.
An example **sql_settings.conf** file:

    localhost,myTestDatabase,benfulcher,myInsecurePassword

Once you have configured your **sql_settings.conf** file, and you can run `dbc = SQL_opendatabase;` and `SQL_closedatabase(dbc)` without errors, then you can smile to yourself and you should at this point be happy because Matlab can communicate successfully with your mySQL server!
You should also be excited because you are now ready to set up the database structure!

Note that if your database is not set up on your local machine (i.e., `localhost`), then Matlab can communicate with a mySQL server through an ssh tunnel, which requires some additional setup (described below).

Note also that the `SQL_opendatabase` function uses Matlab's *Database Toolbox* if a license is available, but otherwise will use java commands; both are supported and should give identical operational behavior.

## Changing to a new database

## Setting up an ssh tunnel to a mySQL server
<!-- {#sec:sqlssh} -->

In some cases, the mySQL server you wish to connect to requires an ssh tunnel.
One solution is to use port forwarding from your local machine to the server.
The port forward can be set up in the terminal using a command like:

```bash
    ssh -L 1234:localhost:3306 myUsername@myServer.edu
```

This command connects port 1234 on your local computer to port 3306 (the default mySQL port) on the server.
Now, telling Matlab to connect to `localhost` through port 1234 will connect it, through the established ssh tunnel, to the server.
This can be achieved by specifying the server as `localhost` and the port number as 1234 in the `sql_settings.conf` file (or during the `install` process).