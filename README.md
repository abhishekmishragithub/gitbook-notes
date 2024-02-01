---
description: Some notes/code snippet related to pandas operations.
---

# Pandas notes

## Apply a function with multiple arguments to pandas DataFrame columns - using DataFrame.apply()

`DataFrame.apply()` applies a function along an axis of the DataFrame.&#x20;

Following is the syntax:

```python
DataFrame.apply(func, axis=0, raw=False, result_type=None, args=(), **kwds)
```

{% hint style="info" %}
Reference: [https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.apply.html](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.apply.html)
{% endhint %}

Use `args=(param1, param2, )` parameter in `DataFrame.apply()` method to pass additional arguments to a function.

It takes function parameters in a tuple. The first argument is series by default so you don't need to pass it.

**Example:** Convert an ISO date/DateTime stamp (_`2021-04-12T07:21:00.345344`_) to standard  \
(`2021-04-10 07:21:00.345344`) format.

{% code title="apply_test.py" %}
```python
import pandas as pd

def date_to(row, col_name):
''' Function to convert an ISO date format to standard date format'''
        row[col_name] = pd.to_datetime(row[col_name]).strftime('%Y-%m-%d %H:%M%:%S.%f')
        #row[col_name] = pd.to_datetime(row[col_name]).strftime('%Y-%m-%d %H:%M%:%S.%f')[:-3]
        return row[col_name]


sample_dates = {
            "Date": [
                "2021-04-12T07:21:00.345344",
                "2015-10-19T07:18:00.234234",
                "2018-11-19T07:15:00.423423",
            ],
            "AnotherDate": [
                "2018-12-22T07:21:00.963344",
                "2019-08-19T07:18:00.887234",
                "2020-03-20T07:15:00.765423",
            ]
        }
        
df = pd.DataFrame(s2)
# df['Date'] = df.apply(date_to, axis=1) :apply function without paramter.
df["Date"] = df.apply(date_to, args=("Date",), axis=1) # apply function with paramter.
df["AnontherDate"] = df.apply(date_to, args=("AnontherDate",), axis=1)
```
{% endcode %}

#### Output**:**

```python
                         Date                AnotherDate
0  2021-04-12 07:21:00.345344  2018-12-22 07:21:00.963344
1  2015-10-19 07:18:00.234234  2019-08-19 07:18:00.887234
2  2018-11-19 07:15:00.423423  2020-03-20 07:15:00.765423
```
