---
layout: post
title:      "pandas.DataFrame Quick-Start Guide (Python)"
date:       2020-07-24 16:48:14 -0400
permalink:  pandas_dataframe_quick-start_guide_python
---

The package `pandas` is oft-used in Flatiron's Data Science course.  Its manipulation of dataframes is very handy, although there are some surprising limitations that further differentiate it from working with simple Python or Java matrices.  This post will address workarounds for a few of those limitations as well as the existing useful simple coding available when working with a `pandas` dataframe.  Links appear throughout that go directly to the relevant webpage of the documentation.

### Creating a DataFrame

`import pandas as pd #pd is the usual alias`

My preferred way is to [read in an existing .CSV file](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html):

`df = pd.read_csv(“filepath.csv”, index_col=[0]) #index_col specifies a column to become the dataframe's index`

And my second-preferred way is to use a dictionary:

```
dfdict = {"col1":[3,4,1,2],"col2":["first","second","third","fourth"],"col3":["2020-07-02","2020-05-05","2020-02-02","2020-03-24"]}
fouritems=range(3,7)
df = pd.DataFrame(dfdict,index=fouritems)
```
The keys become the column names and the values become the items in their respective columns.
My third-preferred method is with a matrix of rows, and one parameter with each the corresponding column and row names or values:

```
fouritems=range(3,7)
pd.DataFrame([[3,"first","2020-07-02"],[4,"second","2020-05-05"],[1,"third","2020-02-02"],[2,"fourth","2020-03-24"]],columns=["col1","col2","col3"],index=fouritems)
```
The index can be any list kind of object of numbers or Strings or combination of them although an index will be automatically created if not provided, starting at 0.

If a label needs to change, columns and rows can be [renamed](https://pandas.pydata.org/pandas-docs/version/0.21/generated/pandas.DataFrame.rename.html) with:

`df.rename({"col2":"ordinal"}, axis="columns")`

### Adjusting rows and columns

One line of code filters the dataframe for specific columns or rows, even those that meet certain criteria:
```
df[df["col3","col2"]]
df[df["col1"]>2]
```
The latter is a dataframe with only those rows that have a value for "col1" that is greater than 2.

Another convenient function is adding another column onto the dataframe.  Below are two ways to create “col4”, a product of the “col1” value at each corresponding index multiplied by 3:

1. `df["col4"]=df["col1"]*3`
2. `df = pd.concat(data=[df,df["col1"]*3],axis="columns")`

An alternative way to refer to columns is like an attribute, although new columns cannot be created with this syntax:
```
df.col2
```
It's neat to refer to columns with minimal punctuation, but it becomes impractical for looping through columns or if column names contain special characters. For those cases, it's recommended to do:
```
def somefunction(dataframe):
     for colname in dataframe.columns:
           dataframe[colname]
```
`df.columns` returns a list of the column names in the order they appear from left-to-right on the top of dataframe `df`.  The code would run into trouble with the phrasing `dataframe.colname`, since this would be like writing `dataframe."col1"`, which is not recognized.

An important caveat when manipulating dataframes: performing a filter, sort or other `pandas` function does not usually save the dataframe with that condition unless it has the parameter `inplace=True`.  By default, `inplace` is set to `False`.  The examples in this blog post largely do not include it so when copying and pasting this code, your dataframe will not be changed when trying out different functions. Below, two ways to save a sorted version of `df` as `df`:
1. `df.sort_values(by=”col1”, inplace=True)`
2. `df = df.sort_values(by=”col1”)`

The index does not change for either of these, but an index can be set to a specific column with `df.set_index(“col1”)` (and with `inplace=True`).

I find rows less convenient to navigate than columns, especially since index values do not update/change unless explicitly coded. In order to add a new row to the dataframe, it's simplest to treat the row as a new DataFrame when using [append](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.append.html#pandas.DataFrame.append) and provide an index value or set `ignore_index=True` (the default is `False`):
```
newrow={"col1":9,"col2":"fifth","col3":"2020-10-10"} #making it a dictionary, similar in style to creating a dataframe
df.append(newrow, ignore_index=True)
```
However `ignore_index=True` will reset the index to 0, 1, 2, etc. To add a new row without changing the index, the row needs to completely be a new dataframe first:
```
newrowdf = pd.DataFrame([[9,"fifth","2020-10-10"]],columns=["col1","col2","col3"],index=[7])
df.append(newrowdf)
```
The nested list [[]] is necessary to indicate that these three objects are one row. If adding more than one row, it then takes the form of [[newrow],[newrow2],....[lastnewrow]], and the `index` would also need the exact number of values to match [7,nextidx, . . .lastidx].

### Differentiating between `.loc` (location by labels) and `.iloc` (location by indices)

To loop through a column’s entries and keep track of the index and column, use [`.loc`](https://pandas.pydata.org/pandas-docs/version/0.21/generated/pandas.DataFrame.loc.html):
```
for idx, row in zip(df.index,df.values):#to loop row by row
           for col in df.columns: #for each column name
                   df.loc[idx, col]
```

With `.loc`, specifying number 3 corresponds to the record with 3 as its index, *as a label*, whereas [`.iloc`](https://pandas.pydata.org/pandas-docs/version/0.21/generated/pandas.DataFrame.iloc.html) will return the record in the fourth (0,1,2,*3*,4,5) location in the index:
```
df.loc[3]
df.iloc[3]
```
I think of `.iloc` as allowing for functionality similar to lists and matrices, relying on **integers**, and `.loc` relying on the **names or labels** of columns and indices. As such, `.loc` can not access the last index with -1 unless the last row literally has an index label of -1.  Also, the `.loc` slice is inclusive of the stop value, unlike Python lists’ slices:
```
df.loc[4:6]
df.loc[:4, "col2":]
```
However, `.iloc` is exclusive of a slice’s stop value and allows for the following:
```
df.iloc[-2]
df.iloc[:2,1:]
```

### Datetime datatype in pandas

Possibly my favorite datatype in `pandas` is the `datetime` object.
`df.info()`
This provides details for every column (unless there are more than a certain number, which can be specified with `max_cols` or using `verbose=True`) about which datatype a column is and how many items are in it that are not null (or NaN) values.

Columns or the entire dataframe [can be converted](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Series.astype.html) to different datatypes with `astype`:
```
df.astype({"col2":"category", "col3":"datetime64"})
df.astype("object")
```
Or directly to the `datetime` datatype:
`df["col3"] = pd.to_datetime(df.col3)`

In `datetime` conversion in pandas, conveniently, the dates don’t all need to be in the same format; [it can](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.to_datetime.html) recognize several different variations (“Jun 15 2020”, “2020/06/15” and “15.06.2020” would be equivalent).  However, if the dates are in the format day-month-year, use the `pd.to_datetime` with the parameter `dayfirst=True`. Although the default is for month-day-year, if the first value is an impossible month, it will try to convert it as day-month-year, and vice versa if `dayfirst` is True.

`pd.to_datetime([“11-15-2020”],dayfirst=True)`

By itself, this is parsed as Nov. 15, 2020 and would be converted to `DatetimeIndex(['2020-11-15'], dtype='datetime64[ns]', freq=None)`.

A list of datatypes are in the [pandas documentation](link to datatypes), but the main ones that .CSV data are read in as are: `int64`, `Object`, and `float64`.  However, [`.read_csv`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html) has the parameter `parse_dates` which can take a list of columns to be read in as `TimeStamp` objects, as well as related parameters (like `dayfirst`).  I usually think of the `Object` type as a `String` and code accordingly.

`df.info()`

will show that the type for `col3` is now `datetime64[ns]`.  Conveniently, the month, year, quarter and [other qualities](https://pandas.pydata.org/pandas-docs/stable/getting_started/basics.html#basics-dt-accessors) like `dayofyear` can be derived from it:

`df.col3.dt.dayofyear`

There [is](https://pandas.pydata.org/pandas-docs/stable/getting_started/intro_tutorials/09_timeseries.html#min-tut-09-timeseries)。[plenty](https://pandas.pydata.org/pandas-docs/stable/user_guide/timeseries.html#timeseries-components)。[of](https://pandas.pydata.org/pandas-docs/stable/user_guide/timeseries.html#timeseries)。[functionality](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.to_timedelta.html#pandas.to_timedelta) with datetime objects. Also, when set as the index, `"col3"` becomes a [`DateTimeIndex`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DatetimeIndex.html):

`df.set_index(“col3”).index.month`

and the same functionality available through `.dt.` is now through `.index.`.

### Managing inconsistencies in the data
One way to create an empty dataframe (of null values, only) with desired column names and index values, is with one mention of a null value, which becomes every entry:
```
import numpy as np #using the usual alias
df = pd.DataFrame(data=np.nan,col=[”c1”,”c2”],index=range(0,20)) #numpy's null value
df.shape
```
This dataframe has two columns that each contain 20 entries and `.info()` will say that both have 0 non-null values of datatype `float64`.
Two usual options to handle inconsistencies in data are to drop the values or fill them with an alternative.  In `pandas`, they are [`.drop()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.drop.html) and [`.fill()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.fill.html) but for the specific cases of null values, `pandas` has the [following](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.dropna.html):
```
df.dropna(axis="columns") #drops columns that have any null values
df.fillna(value=”nonexistent”) #fills every null value with the String “nonexistent”
df.fillna(method=”ffill”) #fills null values with the closest non-null value in a row before it (“forward filling”)
```
Conversely, `“bfill”` (“backward filling”) fills null values with the closest non-null value in a row after it.  [`.fillna`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.fillna.html) also allows for specifying which value should be filled in which column using a dictionary (although if a column name is omitted, null values in that column will not be filled):

`df.fillna(value={“c1”:24,”c2”:”hiya”})`

You can specify whether rows that contain null values can be dropped or if it should be the column, with `axis`, and how many non-null values qualify a row or column to be kept, with `thresh`:

`df.dropna(axis=”index”,thresh=3) #keeps rows that have at least 3 values that are not NaN`

Or use `how` to determine whether a row or column containing ‘any’ or ‘all’ null values is dropped; the default is 'any':

`df.dropna(axis=”columns”,how=”all”) #drops the columns that consist entirely of null values`

Although before dropping or filling null values, you can get a sense of how many there are in the dataframe.  `.info()` shows how many non-null values are in each column, but `pandas` also has a specific method in `.isna`. It returns 1’s and 0’s for whether a value is null or not, respectively, and so summing them up gives an answer:

`sum(df.isna()) #gives totals for each column`

Another case of inconsistencies in data could be if the DataFrame has duplicate rows.  If duplicate columns are the concern, the dataframe could be transposed with the simple `.T` and then keep only the first-appearing one:
```
transposed_df = df.T
transposed_df.drop_duplicates(keep=”first”, inplace=True)
df = transposed_df.T
```
If you’re only interested in keeping one of each kind in a certain column, this will keep only the last-appearing duplicate in `col3`:

`df.drop_duplicates(subset=”col3”,keep=”last”)`

### Wrap-up

Finally, a dataframe can be saved as a .CSV file:
`df.to_csv(“file_path.csv”)`

I would encourage exploring `pandas` by chaining together methods and using your preferred search engine for ways to do what you're thinking of, as I find browsing the [documentation website](https://pandas.pydata.org/pandas-docs/stable/) to be slightly unintuitive.
