# Setting up the mySQL database

We assume that the user has access to and appropriate read/write privileges for a local or network _mySQL_ server database. Instructions on how to install and set up a _mySQL_ database on a variety of operating systems can be found [here](http://dev.mysql.com/doc/refman/5.7/en/installing.html).

## Setting Matlab up to talk to a mySQL server using the java connector

Before the structure of the database can be created, Matlab must be set up to be able to talk to the mySQL server, which requires installing a mySQL java connector. The steps required to achieve this are performed by the script `install_jconnector`, which should be run from the main _hctsa_ directory. If this script runs successfully and a mySQL server has been installed \(either on your local machine or on an external server, see above\), you are then ready to run the `install` script.

The following outlines the actions performed by the `install_jconnector` script \(including instructions on how to perform the steps manually\):

It is necessary to relocate the J connector from the **Database** directory of this code repository \(which is also freely available [here](http://dev.mysql.com/downloads/connector/j/)\): the file `mysql-connector-java-5.1.35-bin.jar` \(for version 5.1.35\). Instructions are here and are summarized below, and described in the [Matlab documentation](http://www.mathworks.co.uk/help/matlab/matlab_external/bringing-java-classes-and-methods-into-matlab-workspace.html). This .jar file must be added to a static path where it can always be found by Matlab. A good candidate directory is the **java/jarext/** subdirectory of the Matlab root directory \(to determine the Matlab root directory, simply type `matlabroot` in an open Matlab command window\).

For Matlab to see this file, you need to add a reference to it in the **javaclasspath.txt** file \(an alternative is to modify the **classpath.txt** file directly, but this may not be supported by newer versions of Matlab\). This file can be found \(or if it does not exist, should be created\) in Matlab’s preferences directory \(to determine this location, type `prefdir` in a command window, or navigate to it within Matlab using `cd(prefdir)`\).

This **javaclasspath.txt** file must contain a text reference to the location of the java connector on the disk. In the case recommended above, where it has been added to the **java/jarext** directory, we would add the following to the **javaclasspath.txt** file:

```text
    $matlabroot/java/jarext/mysql-connector-java-5.1.35-bin.jar
```

ensuring that the version number \(5.1.35\) matches your version of the J connector \(if you are using a more recent version, for example\).

Note that **javaclasspath.txt** can also be in Matlab’s startup directory \(for example, to modify this just for an individual user\).

After restarting Matlab, Matlab should then have the ability to communicate with _mySQL_ servers \(we will check whether this works below\).

## Installing the Matlab/mySQL system

The main tasks involved in installing the Matlab/mySQL interface are achieved by the `install.m` script, which runs the user through the steps below.  

### Creating the mySQL database

If you have not already done so, creating a mySQL database to use with Matlab can be done using the `SQL_create_db` function. This requires that mySQL is installed on an accessible server, or on the local machine \(i.e., using `localhost`\). If the database has already been set up, then you do not need to use the `SQL_create_db` function but you must then create a text file, `sql-setting.conf`, in the **Database** directory of the repository. This file contains four comma-delimited entries corresponding to the server name, database name, username, and password, as per the following:

```text
hostname,databasename,username,password
```

The settings listed here are those used to connect to the mySQL server. Remember that your password is sitting here in this document in unencrypted plain text, so do not use a secure or important password.

To check that Matlab can connect to external servers using the mySQL J-connector, using correct host name, username, and password settings, we introduce the Matlab routines `SQL_OpenDatabase` and `SQL_CloseDatabase`. An example usage is as follows:

```text
    % Open a connection to the default mySQL database as dbc:
    % Connection details are stored in sql-setting.conf
    dbc = SQL_OpenDatabase;

    % <Do things with the database connection, dbc>

    % Close the connection, dbc:
    SQL_CloseDatabase(dbc);
```

For this to work, the **sql\_settings.conf** file must be set up properly. This file specifies \(in unencrypted plain text!\) the login details for your mySQL database in the form `hostName,databaseName,username,password`.

An example **sql\_settings.conf** file:

```text
localhost,myTestDatabase,benfulcher,myInsecurePassword
```

Once you have configured your **sql\_settings.conf** file, and you can run `dbc = SQL_OpenDatabase;` and `SQL_CloseDatabase(dbc)` without errors, then you can smile to yourself and you should at this point be happy because Matlab can communicate successfully with your mySQL server! You should also be excited because you are now ready to set up the database structure!

Note that if your database is not set up on your local machine \(i.e., `localhost`\), then Matlab can communicate with a mySQL server through an ssh tunnel, which requires some additional setup \(described below\).

Note also that the `SQL_OpenDatabase` function uses Matlab's _Database Toolbox_ if a license is available, but otherwise will use java commands; both are supported and should give identical operational behavior.

## Changing between different databases

To start writing a new dataset to a new database, or start retrieving data from a different database, you will need to change the database that Matlab is configured to connect to. This can be done using the `SQL_ChangeDatabase` script \(which walks you through the steps and writes over the existing **sql\_settings.conf** file\), or by altering the **sql\_settings.conf** file directly.

Note that one can swap between multiple databases easily by commenting out lines of the **sql\_settings.conf** file \(adding `%` to the start of a line to comment it out\).

## Setting up an ssh tunnel to a mySQL server

In some cases, the mySQL server you wish to connect to requires an ssh tunnel. One solution is to use port forwarding from your local machine to the server. The port forward can be set up in the terminal using a command like:

```bash
    ssh -L 1234:localhost:3306 myUsername@myServer.edu
```

This command connects port 1234 on your local computer to port 3306 \(the default mySQL port\) on the server. Now, telling Matlab to connect to `localhost` through port 1234 will connect it, through the established ssh tunnel, to the server. This can be achieved by specifying the server as `localhost` and the port number as 1234 in the **sql\_settings.conf** file \(or during the `install` process\), which can be specified as the \(optional\) fifth entry, i.e.,:

```text
hostname,databasename,username,password,1234
```

