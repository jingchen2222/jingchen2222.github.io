---
layout: "post"
title: "Merge, join, and concatenate"
date: "2016-11-07 19:58"
tags:
  - pandas
  - 深度学习
  - merge
  - study

categories:
  - study
  - 深度学习
  - pandas
---

## Concatenating objects

> The concat function (in the main pandas namespace) does all of the heavy lifting of performing concatenation operations along an axis while performing optional set logic (union or intersection) of the indexes (if any) on the other axes. Note that I say “if any” because there is only a single possible axis of concatenation for Series.

<!-- More -->

``` python
pd.concat(objs, axis=0, join='outer', join_axes=None, ignore_index=False,
          keys=None, levels=None, names=None, verify_integrity=False,
          copy=True)
```

### argument:

- objs: a sequence or mapping of Series, DataFrame, or Panel objects.
    - when is dict
        - keys = keys of dict
    - None objects will be dropped silently unless they are all None. (in which case a ValueError will be raised.)
- axis : {0, 1, ...}, default 0.
    - 0,  to concatenate along rows
    - 1, concat along cols
- join : {‘inner’, ‘outer’},How to handle indexes on other axis(es).
    - default ‘outer’.
    - outer for union
    - inner for intersection.
- ignore_index : boolean, default False.
    - If True, do not use the index values .
- join_axes : list of Index objects. Specific indexes to use for the other n - 1 axes instead of performing inner/outer set logic.
- keys : sequence, default None.
    - when hierarchical index, as the outermost level.
    - If multiple levels passed, should contain tuples.
    - use keys when we want to associate specific keys with each of the pieces of the chopped up DataFrame
    - resulting object’s index has a hierarchical index.
- levels : list of sequences, default None.
    - use for constructing a MultiIndex.
    - when None, they will be inferred from the keys.
- names : list, default None.
    - Names for the levels in the resulting hierarchical index.
- verify_integrity : boolean, default False.
    - Check duplicates.
- copy : boolean, default True.
    - If False, do not copy data unnecessarily.

### Set logic on the other axes

- Take the (sorted) union of them all, join='outer'.

    - ALL INDEX KEEP
- Take the intersection, join='inner'.

    - KEEP intersection indexes
- Use a specific index BY join_axes argument

    -  reuse the exact index from the original DataFrame

### More concatenating with group keys

> A fairly common use of the keys argument is to override the column names when creating a new DataFrame based on existing Series. Notice how the default behaviour consists on letting the resulting DataFrame inherits the parent Series’ name, when these existed.

> Through the keys argument we can override the existing column names.

### Appending rows to a DataFrame

#### Keys:

- Append a row **by passing a Series or dict** to append
- use ignore_index with this method to instruct DataFrame to discard its index

### Concatenating using append
> A useful shortcut to concat are the append instance methods on Series and DataFrame.

### Database-style DataFrame joining/merging

> pandas has full-featured, high performance in-memory join operations idiomatically very similar to relational databases like SQL. These methods perform significantly better (in some cases well over an order of magnitude better) than other open source implementations (like base::merge.data.frame in R). The reason for this is careful algorithmic design and internal layout of the data in DataFrame.

- hight performance, join operations, like SQL
- better than other open source implementations
- might be interested in a comparison with SQL.
- merge
``` python
pd.merge(left, right, how='inner',
on=None, left_on=None, right_on=None,
left_index=False, right_index=False,
sort=True, suffixes=('_x', '_y'),
copy=True, indicator=False)
```

### argument

- left
- right
- how
- on:
    - Columns (names) to join on
    - both in left and right
- left_on
    - from left cols
    - use as keys
- right_on
    - from right cols
    - use as keys
- left_index
    - if true: use the index (row labels) from the left DataFrame as its join key(s).
- right_index
    - if true: use the index (row labels) from the right DataFrame as its join key(s).
- how
    - One of 'left', 'right', 'outer', 'inner'
- sort
    - if true, Sort the result DataFrame by the join keys
- suffixes
- copy
- indicator

### Joining a single Index to a Multi-index

> You can join a singly-indexed DataFrame with a level of a multi-indexed DataFrame. The level will match on the name of the index of the singly-indexed frame against a level name of the multi-indexed frame.

### Joining with two multi-indexes
### Overlapping value columns
### Joining multiple DataFrame or Panel objects
### Merging together values within Series or DataFrame columns

## Timeseries friendly merging

### Merging Ordered Data

> A merge_ordered() function allows combining time series and other ordered data. In particular it has an optional fill_method keyword to fill/interpolate missing data:

- merge_ordered
- combining time series and other ordered data
- fill_method fill/interpolate missing data:

### Merging AsOf

- merge_asof method
- match on nearest key rather than equal keys
- tolerance, e.g. tolerance=pd.Timedelta('2ms')
