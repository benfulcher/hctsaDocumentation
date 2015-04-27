# Clearing or removing data

Sometimes you might wish to remove a problematic set of time series from the database (that might have been input incorrectly, for example), including their metadata, and all records of computed data.
Other times you might find a problem with the implementation of one of the operations. In this case, you would like to retain that operation in the database, but flush all of the data currently computed for it (so that you can recompute new values).
Both of these types of tasks (both removing and clearing time series or operations) can be achieved with the function `SQL_clear_remove`.

This function takes in information about whether to clear (clear any calculated data) or remove (completely delete the given time series or operations from the database).

Example usages are given below:
```matlab
% Clear time series with ts_ids in the range 10-15
SQL_clear_remove('ts',10:15,0);

% Remove time series with ts_ids in the range 10:15
SQL_clear_remove('ts',10:15,1);

% Remove time series with ts_ids in the range 10:15
SQL_clear_remove('ts',10:15,1);
```
