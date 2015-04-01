# Plotting the time series

The *hctsa* package provides a simple means of plotting time series, via the `TSQ_plot_timeseries` function.

For example, to plot a set of time series that have not been assigned groups, we can run the following:

    whatData = 'norm'; % Get data from HCTSA_N.mat
    plotWhatTimeSeries = 'all'; % plot examples from all time series
    plotHowMany = 10;
    TSQ_plot_timeseries('norm','all',10);