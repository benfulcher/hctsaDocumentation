# Highly Comparative Time-Series Analysis: Manual

Guide to implementing the techniques and methods developed in [Highly Comparative Time-Series Analysis](http://rsif.royalsocietypublishing.org/content/10/83/20130048.full) using an interface between Matlab and a _mySQL_ server.

## Introduction {#sec:introduction}
This document outlines steps required to set up and implement highly comparative analysis methods on a system using an interface between Matlab and a _mySQL_ database using the highly comparative time-series analysis repository.
The document is an accompaniment this code repository.

A summary of the steps involved in setting up the system is as follows, and will be elaborated on below:

1. Install and set up a mySQL server, setup a new database to store Matlab calculations in, and set up Matlab to be able to communicate with it, [here](sec:SettingUp).
2. Populate the database with master operations, operations, and time series (using the **install.m** script followed by the user importing their own time series using `SQL_add`), [here](sec:PopulatingDatabase).
3. Run computations to evaluate the operations on all the time series using the **sample_runscript** as a template, [here](sec:calculating).
4. Analyze the results of computations by retrieving calculated data using **TSQ_prepared**, normalizing and filtering it using **TSQ_normalize**, and then making use of a range of analysis and visualization scripts provided, Sec.~\ref{sec:analyzing}.


3.  Run computations to evaluate the operations on all the time series
    using the **sample_runscript** as a template,
    Sec. [sec:calculating].

4.  Analyze the results of computations by retrieving calculated data
    using **TSQ_prepared**, normalizing and filtering it using
    **TSQ_normalize**, and then making use of a range of analysis and
    visualization scripts provided, Sec. [sec:analyzing].