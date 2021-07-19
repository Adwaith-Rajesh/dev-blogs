Introducing OnstroDB.

## Installation

```commandline
pip install onstro-db
```

### Let's see how fast it is.

In this example we can make a DB with 4 columns and in which 2 columns have a default value. Which are added during **Runtime**.

The test code

```python
from onstrodb import OnstroDb
import time

test_schema = {
    "name": {"type": "str", "required": True},
    "age": {"type": "int", "required": True},
    "place": {"type": "str", "default": "canada"},
    "alive": {"type": "bool", "default": True}
}

db = OnstroDb(db_name="test", in_memory=True, schema=test_schema)


# generate the data to insert
data = []
for i in range(10_000):
    print(i, end="\r", flush=True)
    d = {"name": f"ad{i}", "age": i}
    data.append(d)


# insert the data into the DB
start = time.time()
db.add(data)
stop = time.time()

print("Elapsed Time: ", stop - start)

```

Here we first create all the data we want to insert and the we time the insertion process.

Here are the results.

```commandline
Elapsed Time:  1.077016830444336
```

Onstro-Db supports full CRUD operation, comes with a simple CLI, hash strict schema, and also prevent data duplication by default.

### As you can see this thing is fast. To know more visit the github repo [here](https://github.com/Adwaith-Rajesh/onstro-db).

Star and follow the repo for future updates.
