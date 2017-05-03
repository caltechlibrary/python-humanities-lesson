---
title: Working with Web Data in Python
teaching: 30
exercises: 30
questions:
    - " How can I use data to get specific online content? "
objectives:
    - Be able to deal with Nan values.
    - Understand and set data types.
    - Be able to get web-based content.
    - Use a basic loop.
---

# Not Formatted

This lesson has not been formatted and serves as a rough outline of the content that will be covered.

```python
import requests as rq
from PIL import Image
from io import BytesIO
import pandas as pd
```


```python
#Read in file
cushman_df = pd.read_csv("cushman_encoded.csv")
# Warning is because pandas is having trouble assigning types to the data
# This is not a failure, just something we'll be looking at in the next section
# Alternative way of resolving error - add dtype="object" to set everything as string
print(cushman_df.columns)
```

    Index(['PURL', 'IU Archives Number', 'Cushman number', 'Roll ID',
           'Roll number', 'Year', 'Frame number', 'Start Date', 'End Date',
           'Archive Date', 'Month Unknown', 'Day Unknown', 'Camera settings',
           'Asterisk', 'Description from Notebook', 'Description from Slide Mount',
           'Image Note', 'No Slide Exists', 'Slide Condition', 'ID',
           'Street Address', 'Street Address 2', 'Street', 'Street 2', 'Street 3',
           'Street 4', 'Street 5', 'Neighborhood', 'City', 'City 2', 'City 3',
           'City 4', 'County', 'County 2', 'County 3', 'County 4',
           'State/Province', 'State/Province 2', 'State/Province 3',
           'State/Province 4', 'Country', 'Country 2', 'Country 3', 'Country 4',
           'Personal Names 1', 'Personal Names 2', 'Personal Names 3',
           'Personal Names 4', 'Personal Names 5', 'Personal Names 6',
           'Personal Names 7', 'Personal Names 8', 'Corporate Names 1',
           'Corporate Names 2', 'Corporate Names 3', 'Corporate Names 4',
           'Corporate Names 5', 'Corporate Names 6', 'Corporate Names 7',
           'Corporate Names 8', 'Other Names 1', 'Other Names 2', 'Other Names 3',
           'Other Names 4', 'Genre 1', 'Genre 2', 'Genre 3', 'Genre 4',
           'Topical Subject Headings 1', 'Topical Subject Headings 2',
           'Topical Subject Headings 3', 'Topical Subject Headings 4',
           'Topical Subject Headings 5', 'Topical Subject Headings 6',
           'Topical Subject Headings 7', 'Topical Subject Headings 8',
           'Topical Subject Headings 9', 'Topical Subject Headings 10',
           'Topical Subject Headings 11', 'Topical Subject Headings 12', 'Other 1',
           'Other 2', 'Other 3', 'Other 4', 'Other 5', 'Other 6', 'Other 7',
           'Other 8', 'ColorCorrected', 'LastUpdated'],
          dtype='object')


    /Users/tmorrell/anaconda/lib/python3.5/site-packages/IPython/core/interactiveshell.py:2717: DtypeWarning: Columns (25,26,39,43,48,57,58,86) have mixed types. Specify dtype option on import or set low_memory=False.
      interactivity=interactivity, compiler=compiler, result=result)



```python
#We want the Identifier
print(cushman_df['IU Archives Number'][0:5])
```

    0    1411.0
    1    1412.0
    2    1413.0
    3    1414.0
    4    1415.0
    Name: IU Archives Number, dtype: float64



```python
#It shouldn't be a float
#Let's convert it to int
cushman_df['IU Archives Number'] = cushman_df['IU Archives Number'].astype('int')
```


    ---------------------------------------------------------------------------

    ValueError                                Traceback (most recent call last)

    <ipython-input-4-b124fd8598e1> in <module>()
          1 #It shouldn't be a float
          2 #Let's convert it to int
    ----> 3 cushman_df['IU Archives Number'] = cushman_df['IU Archives Number'].astype('int')
    

    /Users/tmorrell/anaconda/lib/python3.5/site-packages/pandas/core/generic.py in astype(self, dtype, copy, raise_on_error, **kwargs)
       3052         # else, only a single dtype is given
       3053         new_data = self._data.astype(dtype=dtype, copy=copy,
    -> 3054                                      raise_on_error=raise_on_error, **kwargs)
       3055         return self._constructor(new_data).__finalize__(self)
       3056 


    /Users/tmorrell/anaconda/lib/python3.5/site-packages/pandas/core/internals.py in astype(self, dtype, **kwargs)
       3187 
       3188     def astype(self, dtype, **kwargs):
    -> 3189         return self.apply('astype', dtype=dtype, **kwargs)
       3190 
       3191     def convert(self, **kwargs):


    /Users/tmorrell/anaconda/lib/python3.5/site-packages/pandas/core/internals.py in apply(self, f, axes, filter, do_integrity_check, consolidate, **kwargs)
       3054 
       3055             kwargs['mgr'] = self
    -> 3056             applied = getattr(b, f)(**kwargs)
       3057             result_blocks = _extend_blocks(applied, result_blocks)
       3058 


    /Users/tmorrell/anaconda/lib/python3.5/site-packages/pandas/core/internals.py in astype(self, dtype, copy, raise_on_error, values, **kwargs)
        459                **kwargs):
        460         return self._astype(dtype, copy=copy, raise_on_error=raise_on_error,
    --> 461                             values=values, **kwargs)
        462 
        463     def _astype(self, dtype, copy=False, raise_on_error=True, values=None,


    /Users/tmorrell/anaconda/lib/python3.5/site-packages/pandas/core/internals.py in _astype(self, dtype, copy, raise_on_error, values, klass, mgr, **kwargs)
        502 
        503                 # _astype_nansafe works fine with 1-d only
    --> 504                 values = _astype_nansafe(values.ravel(), dtype, copy=True)
        505                 values = values.reshape(self.shape)
        506 


    /Users/tmorrell/anaconda/lib/python3.5/site-packages/pandas/types/cast.py in _astype_nansafe(arr, dtype, copy)
        529 
        530         if np.isnan(arr).any():
    --> 531             raise ValueError('Cannot convert NA to integer')
        532     elif arr.dtype == np.object_ and np.issubdtype(dtype.type, np.integer):
        533         # work around NumPy brokenness, #1987


    ValueError: Cannot convert NA to integer



```python
#We have NA values - let's ignore them
subset = cushman_df[pd.notnull(cushman_df['IU Archives Number'])].copy()
```


```python
#Now we can 
print(subset['IU Archives Number'].dtype)
subset['IU Archives Number'] = subset['IU Archives Number'].astype('int')
print(subset['IU Archives Number'].dtype)
```

    float64
    int64



```python
#Now we're going to get the images
base = 'http://purl.dlib.indiana.edu/iudl/archives/cushman/full/P0'
#We use a loop to look through the first 10 rows
for i in range(10):
    # we get the record ID number
    number = subset['IU Archives Number'][i]
    # Translate it into a filename
    url = base + str(number) + '.jpg'
    print(url)
    # we use the response library to get the image in bytes
    response = rq.get(url)
    # we use the image library to translate the bytes to an image
    i = Image.open(BytesIO(response.content))
    # and we save the image
    i.save(str(number) + '.jpg')
    print(number)
```

    http://purl.dlib.indiana.edu/iudl/archives/cushman/full/P01411.jpg
    1411
    http://purl.dlib.indiana.edu/iudl/archives/cushman/full/P01412.jpg
    1412
    http://purl.dlib.indiana.edu/iudl/archives/cushman/full/P01413.jpg
    1413
    http://purl.dlib.indiana.edu/iudl/archives/cushman/full/P01414.jpg
    1414
    http://purl.dlib.indiana.edu/iudl/archives/cushman/full/P01415.jpg
    1415
    http://purl.dlib.indiana.edu/iudl/archives/cushman/full/P01416.jpg
    1416
    http://purl.dlib.indiana.edu/iudl/archives/cushman/full/P01417.jpg
    1417
    http://purl.dlib.indiana.edu/iudl/archives/cushman/full/P01418.jpg
    1418
    http://purl.dlib.indiana.edu/iudl/archives/cushman/full/P01419.jpg
    1419
    http://purl.dlib.indiana.edu/iudl/archives/cushman/full/P01420.jpg
    1420

