# Difference of repr and str

```python
class ...(object):
  def __init__(self):
    ...
  def __str__(self):
    ...
  def __repr__(self):
    ...
```



```python
>>> x = 'foo'
>>> repr(x)
"'foo'"
>>> x.__repr__()
"'foo'"
>>> str(x)
'foo'
>>> x.__str__()
'foo'
```

-   ***str() is used for  creating output for end user while repr() is mainly used for debugging  and development. repr’s goal is to be unambiguous and str’s is to be  readable***. For example, if we suspect a float has a small rounding error, repr will show us while str may not.
- `repr()` [compute the “official” string representation of an object ](http://docs.python.org/reference/datamodel.html#object.__repr__) (a representation that has all information about the abject) and `str()` is used to [compute the “informal” string representation of an object](http://docs.python.org/reference/datamodel.html#object.__str__) (a representation that is useful for printing the object).
- The print statement and `str()` built-in  function uses __str__ to display the string representation of the object while the `repr()` built-in function uses __repr__ to display the object. 

