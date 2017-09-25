## Feature comparison

One of the key goals of highly comparative time-series analysis, is to allow unbiased methodological comparison between the vast literature of time-series analysis tools developed for different applications.
By representing features in terms of their outputs across a time-series dataset, the context of a given feature can be assessed by searching the database for features with similar behavior.
The search can be done using a diverse range of real and model-generated data, or using a more specific dataset if this is more appropriate for a given application (e.g., looking just at EEG signals).
Just like [similar time series to a target can be retrieved and visualized](sim_search.md), similar features to a given target feature can also be retrieved using `TS_SimSearch`.

### EXAMPLE: Determining the relationship between a new feature and existing features
We use the example of a new feature, `hot_feature`, and attempt to determine what existing features exhibit similar performance to it.

**1. Setting a data context**
The first step is defining the set of features to compare to (here we use the default *hctsa* library), and the set of time-series data that behavior is going to be assessed on.
If you only ever analyze a particular type of data (e.g., rainfall), then perhaps you're more interested in which methods perform similarly on rainfall data.
If you have just developed a new algorithm for time-series analysis and want to see how it performs across a range of interdisciplinary time-series data, then you may want to use a diverse set of time series sampled from across science.

We have curated a diverse interdisciplinary set of 1000 time series.
Pre-computed results (using [v0.95 of *hctsa*](https://github.com/benfulcher/hctsa/releases/tag/v0.95) and Matlab 2017a) can be downloaded from [figshare](https://figshare.com/articles/1000_Empirical_Time_series/5436136), or features can be recomputed using our input file for the time-series dataset, also on [figshare](https://figshare.com/articles/1000_Empirical_Time_series/5436136) (to ensure implementation consistencies on your local compute architecture; i.e., using `TS_init('INP_Empirical1000.mat');` to initialize, followed by [compute commands involving `TS_compute`](running_computations.md)).
So now we have a context of the behavior of thousands of features on this diverse dataset with which to compare the behavior of our new feature, `hot-feature`.

**2. Computing the new features**
So now we need to compute our three new features, `hot_feature1`, `hot_feature2`, and `hot_feature3`, on the data.
To get the feature values, we could do this directly for isolated computations (using `TS_CalculateFeatureVector`), but in order to maintain the HCTSA structure, we instead produce a new `HCTSA.mat` file containing just `hot_feature` and the same time series.
After generating an input file, `INP_hot_master.txt` containing the function call (using z-scored time series, `y`, and using a second input argument, `'hot'`):
```
MyHotFeature(y,'hot')     hot_master
```

The interesting output of the structure output produced by `MyHotFeature(y,'hot')` is `hotFeature1`, which needs to be specified in another input text file, `INP_hot_features.txt`, for example, as:
```
hot_master.hotFeature1     hot_feature1      nonlinear,hot,sexy
```
where we have given this feature three keywords: `nonlinear`, `hot`, and `sexy`.

So now we are able to initiate a new *hctsa* calculation, specifying custom code calls (*master*) and features to extract from the code call (*features*), as:
```matlab
>> TS_init('INP_Empirical1000.mat','INP_hot_master.txt','INP_hot_features.txt',true,'HCTSA_hot.mat');
```
This generates a new file, `HCTSA_hot.mat`, containing information about the 1000 time series, and the three hot features, which can then be computed as:
```matlab
>> TS_compute(false,[],[],'missing','HCTSA_hot.mat');
```

**3. Combining and comparing**
So now we have both a context of the behavior of a library of >7000 features on 1000 diverse time series, and we also have the behavior of our three hot new features.
It is time to combine them and look for inter-relationships!

```matlab
>> TS_combine('HCTSA_Empirical1000.mat','HCTSA_hot.mat',false,true,'HCTSA_merged.mat');
```

Now that we have all of the data in the same HCTSA file, we can use standard *hctsa* functions to explore; the most relevant is [`TS_SimSearch`](sim_search.md).
We can find the ID assigned to our new `hot_feature` in the merged HCTSA file as:
```matlab
    load('HCTSA_merged.mat','Operations');
    Operations(strcmp({Operations.Name},'my_hot_feature'))
```
which tells us that the ID of `my_hot_feature` in `HCTSA_merged.mat` is 7750.
Then we can use [`TS_SimSearch`](sim_search.md) to explore the relationship of our hot new feature:

```matlab
TS_SimSearch(7750,'tsOrOps','ops','whatDataFile','HCTSA_merged.mat','whatPlots',{'scatter','matrix'})
```

We find that our feature is reproducing the behavior of the first zero of the autocorrelation function (the first match: `first_zero_ac`; see [Interpreting Features](interpreting-features.md) for more info on how to interpret matching features):

![](/assets/Screen Shot 2017-09-25 at 18.43.11.png)

The pairwise distance matrix (distances are $$1-|r|$$, for Pearson correlation coefficients, $$r$$) produced by `TS_SimSearch` provides another visualization of the context of this hot new feature:

![](/assets/Screen Shot 2017-09-25 at 18.42.09.png)

**4. Interpreting**
New features can be incorporated in the default set by adding the necessary master and feature definitions (i.e., the text in `INP_hot_master.txt` and the text in `INP_hot_features.txt`) to the library files (`INP_mops.txt` and `INP_ops.txt` in the **Database** directory of *hctsa*), as explained [here](inputfiles.md).
