## Storing python objects in pysondb


Let's see how we can store an entire python objects, this include strings, list, function, etc.., inside a DB like [pysonDB](https://pypi.org/project/pysondb/)

Consider I've function `foo` which takes an argument `x` and return `x * (x + x)`.

```python
def foo(x: float) -> float:
    return x * (x + x)
```

First things first we nee to convert this object to byte string. To do that we can use the `pickle`. module.

```python
import pickle
byte_string = pickle.dumps(foo)
```

This byte string cannot be directly added to a PysonDB database, as byte strings are not JSON serializable. So a simple workarounds will be to add quotes around the byte string which can be easily done like this.

```python
obj_string = f"{byte_string}"
```

This string can be now added to the DB

```python
from pysondb import db

a = db.getDb("test.json")
a.add({
    "name": "foo_function",
    "obj": obj_string
})
```

To get the object back we can do the following steps.

```python
data = a.getBy({"name": "foo_function"})
```

Now `data` will look something like this.

```python
[{'name': 'foo_function', 'obj': "b'\\x80\\x04\\x95\\x14\\x00\\x00\\x00\\x00\\x00\\x00\\x00\\x8c\\x08__main__\\x94\\x8c\\x03foo\\x94\\x93\\x94.'", 'id': 316182400052721056}]
```

To convert the obj string back to a callable function. We can do the following.

```python
str_obj = data[0]["obj"]
```
Remember that  `str_obj` is still a string and not a byte string, which is what we need, since the required byte string is inside this string, we can simply evaluate the string.

```python
import ast
byte_obj = ast.literal_eval(str_obj) 
```

To call the function we can do this.
```python
call_obj = pickle.loads(byte_obj)
print(call_obj(3))

# output
18
```

## Entire Code
```python
import ast
import pickle

from pysondb import db

def foo(x: float) -> float:
    return x * (x + x)

byte_string = pickle.dumps(foo)
obj_string = f"{byte_string}"

a = db.getDb("test2.json")
a.add({
    "name": "foo_function",
    "obj": obj_string
})

data = a.getBy({"name": "foo_function"})

str_obj = data[0]["obj"]
byte_obj = ast.literal_eval(str_obj)

call_obj = pickle.loads(byte_obj)
print(call_obj(3))

```

So we have successfully stored a python object inside a DB. The steps are the same for all the objects like list or dict.