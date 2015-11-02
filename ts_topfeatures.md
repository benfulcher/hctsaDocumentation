# Determining which features are informative of the class differences using `TS_TopFeatures`

In a time-series classification task, you are often interested in not only determining differences between the labeled classes, but also interpreting them in the context of your application.
For example, an entropy estimate may give low values to the RR interval series measured from a healthy population, but higher values to those measured from patients with congestive heart failure.
When performing a highly comparative time-series analysis, we have computed a large number of time-series features and can use these results to arrive at these types of interpretable conclusions automatically.

A simple way to determine which individual features are useful for distinguishing the labeled classes of your dataset is to compare each feature individually in terms of its ability to separate the labeled classes of time series.
This can be achieved using the `TS_TopFeatures` function.

This function 
