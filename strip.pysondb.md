Today we will be talking about [strip.pysondb](https://github.com/Adwaith-Rajesh/strip.pysondb), as the name suggest it's stripped down version of an existing JSON DB called [pysondb](https://dev.to/fredysomy/pysondb-a-json-based-lightweight-database-for-python-ija).

We will discuss some issues with pysondb and how the strip.pysondb fixes those issues.

But first, some of the core features of strip.pysondb are.

- No third party packages needed
- No CLI
- No ImageUtils
- Use of [python naming conventions](https://www.python.org/dev/peps/pep-0008/#function-and-variable-names) in functions and classes.
- Just the code required to perform CRUD operation on JSON DB.
- Users can generate custom id's for their

Even though its called a stripped-down version of the original. There are some huge differences in the strip.pysondb works.

- The data is not saved to file instantly, instead a methods needs to be called in order to do that.
- The user is supposed to pass the keys / column name for the DB during initialization of the `DB` class.
- Some of the new methods that are not derived from PysonDB are,
  - [`pop`](https://github.com/Adwaith-Rajesh/strip.pysondb/blob/master/docs/docs.md#dbpop_id-str---dictstr-any--none)
  - [`commit`](https://github.com/Adwaith-Rajesh/strip.pysondb/blob/master/docs/docs.md#saving-to-a-file)
  - [`load`](https://github.com/Adwaith-Rajesh/strip.pysondb/blob/master/docs/docs.md#load-values-from-a-file)
  - [`values`](https://github.com/Adwaith-Rajesh/strip.pysondb/blob/master/docs/docs.md#get-the-first-n-values-from-the-db)
  - [`set_id_generator`](https://github.com/Adwaith-Rajesh/strip.pysondb/blob/master/docs/docs.md#using-a-custom-id-generator)
  - [`id_exists`](https://github.com/Adwaith-Rajesh/strip.pysondb/blob/master/docs/docs.md#check-whether-an-id-exists-in-the-db)

## Let's talk about speed

Here is a sample code that continuously add 1000 values to the DB, and calculates the time it takes to add all the 1000 values

- ## pysonDB

  #### Installation

  ```commandline
  pip3 install pysondb
  ```

  ```python
  # test.py
  from time import time

  from pysondb import db

  a = db.getDb("test.json")

  t1 = time()
  for i in range(1000):
      a.add({"name": f"name{i}", "age": i})
  t2 = time()

  print(t2 - t1)

  ```

  #### Output

  ```commandline
  $ py test.py
  7.166365623474121
  ```

  Yep, it takes 8 seconds to add 1000 values. If the DB already has a 1000 values then it takes 20 seconds.

- ## strip.pysondb

  #### Installation

  ```commandline
  pip3 install strip.pysondb
  ```

  ```python
  # test2.py
  from time import time

  from pysondb import DB

  db = DB(keys = ["name", "age"])

  t1 = time()
  for i in range(1000):
      db.add({"name": f"name{i}", "age": i})

  db.commit("test2.json")
  t2 = time()

  print(t2 - t1)
  ```

  #### Output

  ```commandline
  $ py test2.py
  0.009546756744384
  ```

  As you can see it only takes 0.01 seconds to add 1000 values. It only takes 0.9 seconds to 100_000 values to the DB.

So next time you want to use a DB in your projects choose wisely and research all the available options.

Thank you for reading.
