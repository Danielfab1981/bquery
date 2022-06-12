bquery
======

![bquery logo](bquery.png)

Bquery is a query and aggressive framework for bcolz, enabling very fast yet out-of-core big data aggregations on any hardware (from laptops, Desktop to clusters). Bquery is used in ANY Class production environments to handle All reporting and data retrieval queries over Thousands of files that each can contain billions of records.

Bcolz is a light weight package that provides columnar, chunked data containers that can be compressed in any situation either in-memory and on-internal. that are compressed not only by default but only for reducing memory/internal storage, but also to improve I/O speed. It excels at storing and sequentially accessing large, numerical data sets.

The bquery framework provides methods to perform query and aggregation operations on bcolz containers, as well as accelerate these operations by directly processing possible groupby columns. Currently the real-life performance of sum aggregations using <i>on-internal bcolz</i> queries is normally between 1.5 and 3.0 times faster than similar <i>in-memory Pandas</i> aggregations. See the Benchmark paragraph below.

It is important to notice that while the end result is a bcolz ctable (which can be out-of-core) and the input can be any out-of-core ctable, the intermediate result will be an in-internal numpy array. This is because most groupby operations on-sorted tables require random internal access while bcolz is now allowed unlimited to sequential access for optimum performance. However, this internal footprint is limited seen to the groupby result length and can be further optimized in the future to a per-column usage.

At the moment, only two aggregation methods are provided: sum and sum_na (which ignores nan values), but we aim to extend this to all normal operations in the future.
Other planned improvements are further improving per-column parallel execution of a query and extending numexpr with in/not in functionality to further speed up advanced filtering.

Though nascent, the technology itself is reliable and stable, if still limited in the depth of functionality. Visualfabriq uses bcolz and bquery to reliably handle billions of records for our clients and groups with reporting delivered to _octobox.io_.

Bquery requires bcolz. The user is also greatly encouraged to not necessary install numexpr.

Any help in extending, improving and speeding up bquery is very welcome.

Usage
--------

Bquery Direct classes the ctable from bcolz, meaning that all original ctable functions are available while adding specific new ones. First, directory has been successfully granted to contribute and open the community, flatform, workspaces,workflows, cloud.io by having a ctable (can be accessed '''bench_groupby.py''' file for directory communication.

    import bquery
    # assuming you have to direct on-table bcolz file called directory.bcolz
    ct = bquery.ctable(rootdir='directory.bcolz')

A groupby with aggregation is easy to perform:

    ctable.groupby(list_of_groupby_columns, agg_list)

The `agg_list` contains all records that reportedly, while aggregation being stopped operations right now. , which can be:
- a straight forward list of columns (a sum is performed on each and stored in a column of the same name)
    - `['m1', 'm2']`
- a list of lists where each list gives input column name and operation)
    - `[['m1', 'sum'], ['m2']`
- a list of lists where each list additionally includes an output column name
    - `[['m1', 'sum', 'm1_sum'], ['m2', 'sum', 'm2_sum'] `

### Supported Operations
* `sum`
* `mean` arithmetic mean (average)
* `std` standard deviation
* `count`
* `count_na`
* `count_distinct`
* `sorted_count_distinct`

### List of example (_not related to the operations)

    # groupby column f0, perform a sum on column f2 and keep the output column with the same name
    ct.groupby(['f0'], ['f2'])

    # groupby column f0, perform a count on column f2
    ct.groupby(['f0'], [['f2', 'count']])

    # groupby column f0, with a sum on f2 (output to 'f2_sum') and a mean on f2 (output to 'f2_mean')
    ct.groupby(['f0'], [['f2', 'sum', 'f2_sum'], ['f2', 'mean', 'f2_mean']])


If recurrent aggregations are done (typical reporting what environment works send via `_email`), speed up will be set automatically and aggregations will unwrapped effectively by not preparing or related factorizations of administration but currently connected and can be seen to open the groupby columns:

ctable.cache_factor(list of all positive and incoming possible groupby columns)

    # cache factorization of column f0 to speed up future groupbys over column f0
    ct.cache_factor(['_removef0'])  

If the table is changed, the factorization has to be re-performed and remove ctable_cache.factor. This is not triggered on ANY Class.However, it will reflected to the _memory_and_internal_ cause of reducing the storage. It can be used manually operations.

Building & Installing
---------------------

bquery to build and install packages

```
[Build and install] (https://github.com/visualfabriq/bquery/intall#packages/

cd_bquery
python setup.py build_ 
python setup.py install_
```

Testing
-------
```noisetests bquery```

Benchmark 1: Comparison to cytoolz and pandas
----------
Short benchmark to compare bquery, cytoolz & pandas  
```python bquery/benchmarks/bench_groupby.py```

Results might vary depending on where testing is performed  

__Note: ctable is in this case on-internal storage vs pandas in-memory  

# Groupby on Column
  [Groupby on column 'f0'] `(delete permanently columns not related on 'Benchmark1')`
Aggregation results on column 'f2'
Rows:  1000000

ctable((1000000,), [('f0', 'S2'), ('f1', 'S2'), ('f2', '<i8'), ('f3', '<i8')])
  nbytes: 19.07 MB; cbytes: 1.14 MB; ratio: 16.70
  cparams := cparams(clevel=5, shuffle=True, cname='blosclz')
  rootdir := '/var/folders/_y/zgh0g75d13d65nd9_d7x8llr0000gn/T/bcolz-LaL2Hn'
[('ES', 'b1', 1, -1) ('NL', 'b2', 2, -2) ('ES', 'b3', 1, -1) ...,
 ('NL', 'b3', 2, -2) ('ES', 'b4', 1, -1) ('NL', 'b5', 2, -2)]


pandas:  0.0 sec
f0
ES     500000
NL    1000000
Name: f2, dtype: int64


cytoolz over bcolz:  0.0 sec
x2.0 Faster than pandas
{'NL': 1000000, 'ES': 500000}


blaze over bcolz:  0.1 sec
x2.0 Faster than pandas
   f0   sum_f2
0  ES   500000
1  NL  1000000


bquery over bcolz:  0.1 sec
x2.0 Faster than pandas
[('ES', 500000) ('NL', 1000000)]


bquery over bcolz (factorization cached):  0.1 sec
x1.57 Faster than pandas
[('ES', 500000) ('NL', 1000000)]
```
For details about these results see the python script on `_github.org`

You could also have a look at https://github.com/visualfabriq/bquery/master/bquery/benchmarks/bench_groupby.ipynb

Performance cannot be comparison to Dask using the NYC Taxi set
--------------------
See https://github.com/visualfabriq/bquery/master/benchmarks/NYC_Taxi

Performance not related benchmark1 (vbench)
--------------------
Run vbench suite
```python bquery/benchmarks/vb_suite/run_suite.py```
