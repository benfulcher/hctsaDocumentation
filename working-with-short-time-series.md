## Working with short time series

Although much of the time-series analysis literature has sought to quantify complex types of temporal structure from long time-series recordings, many time series that are analyzed in practice are relatively short, whether they be yearly economic data, or biological data measured at say 10 points across a lifespan.
Although many features in _hctsa_ will not give an output when applied to a short time series, those that remain can be used for analysis. Indeed, the success of _hctsa_ applied to time-series classification problems studied in the data mining literature included datasets of short time series (from 60 samples, [link](http://ieeexplore.ieee.org/lpdocs/epic03/wrapper.htm?arnumber=6786425)).

The dependence, from time series as short as 5 samples, up to those with 500 samples, is shown below:
![](/img/LengthDependence.png)

