---
layout: post
title: 'Group By: split-apply-combine'
date: '2016-11-07 17:07'
tags:
  - pandas
  - studynote
  - groupBy
categories:
  - study
  - 深度学习
  - pandas
---

### Filtration：

**doc**: [filter][1]

> The filter method returns a subset of the original object. Suppose we want to take only elements that belong to groups with a group sum greater than 2.

> The argument of filter must be a function that, applied to the group as a whole, returns True or False.


<!-- More -->


#### Keys
- subset
- of original object
- usage
    - argument is function, applied to the group
    - dropna=False,
    - with multiple columns, filters should  specify a column as the filter criterion.

### Flexible apply：

> Use the apply function, which can be substituted for both aggregate and transform in many standard use cases
apply can act as a reducer, transformer, or filter function, depending on exactly what is passed to it.

**Warning**:
>In the current implementation apply calls func twice on the first group to decide whether it can take a fast or slow code path. This can lead to unexpected behavior if func has side-effects, as they will take effect twice for the first group.

**_side-effects_**
>In computer science, a function or expression is said to have a side effect if it modifies some state or has an observable interaction with calling functions or the outside world. For

### Plotting
>Groupby also works with some plotting methods.


### Regrouping by factor**
> Regroup columns of a DataFrame according to their sum, and sum the aggregated ones.

#### Keys:
- re-group col
- according sum
- sum the  aggregated ones

#### resolve

- group col: axis = 1
- according sum: df.sum()
- sum the agg: grouped.sum


``` python
df.groupby(df.sum(), axis=1).sum()
```

### Groupby by Indexer to ‘resample’ data

> Resampling produces new hypothetical samples

> resample to work on indices that are non-datetimelike

**USE**:
> df.index // 5 returns a binary array which is used to determine what get’s selected for the groupby operation.

``` python
df.groupby(df.index // 5).std()
```

### Returning a Series to propagate names

> Group DataFrame columns, compute a set of metrics and return a named Series.

> The Series name is used as the name for the column index.

> useful in conjunction with  stacking in which the column index name will be used as the name of the inserted column:


#### Keys
- compute a set of metrics and return a named Series

``` python
def compute_metrics(x):
  result = {'b_sum': x['b'].sum(), 'c_mean': x['c'].mean()}
  return pd.Series(result, name='metrics')
```


  [1]: http://pandas.pydata.org/pandas-docs/version/0.18.1/generated/pandas.DataFrame.filter.html "filter"
