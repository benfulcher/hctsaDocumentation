# Highly Comparative Time-Series Analysis Manual
A guide to implementing the techniques and methods developed in _Highly Comparative Time-Series Analysis_ using an interface between Matlab and a *mySQL* server.
This manual takes you through the steps required to run highly comparative time-series analysis, as described in our papers:

* [Main highly comparative time-series analysis paper](http://rsif.royalsocietypublishing.org/content/10/83/20130048.full)
* [Application to time-series datamining](http://ieeexplore.ieee.org/lpdocs/epic03/wrapper.htm?arnumber=6786425)
* [Application to fetal heart rate monitoring](http://ieeexplore.ieee.org/xpls/abs_all.jsp?arnumber=6346629)

## Overview
This document outlines steps required to set up and implement highly comparative analysis methods on a system using an interface between Matlab and a _mySQL_ database using the highly comparative time-series analysis repository.
The document is an accompaniment this code repository.

The system can usually be set up by running the `install` script, which installs and sets up a *mySQL* server and database, populates the database with operations, and then compiles all of the mex functions required by Matlab to run all of the operations.

A summary of the steps involved in setting up the system is as follows, and will be elaborated on below:

1. Install and set up a *mySQL* server, setup a new database to store Matlab calculations in, and set up Matlab to be able to communicate with it, [here](#sec:SettingUp).
2. Populate the database with master operations, operations, and time series (using the `install` script followed by the user importing their own time series using `SQL_add`), [here](sec:PopulatingDatabase).
3. Run computations to evaluate the operations on all the time series using the `sample_runscript` as a template, [here](sec:calculating).
4. Analyze the results of computations by retrieving calculated data using `TSQ_prepared`, normalizing and filtering it using `TSQ_normalize`, and then making use of a range of analysis and visualization scripts provided, [here](sec:analyzing).