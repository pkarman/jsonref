# jsonref

[![image](https://github.com/gazpachoking/jsonref/actions/workflows/test.yml/badge.svg?branch=master)](https://github.com/gazpachoking/jsonref/actions/workflows/test.yml?query=branch%3Amaster)
[![image](https://readthedocs.org/projects/jsonref/badge/?version=latest)](https://jsonref.readthedocs.io/en/latest/)
[![image](https://coveralls.io/repos/gazpachoking/jsonref/badge.png?branch=master)](https://coveralls.io/r/gazpachoking/jsonref)
[![image](https://img.shields.io/pypi/v/jsonref?color=%2334D058&label=pypi%20package)](https://pypi.org/project/jsonref)

`jsonref` is a library for automatic dereferencing of [JSON
Reference](https://datatracker.ietf.org/doc/html/draft-pbryan-zyp-json-ref-03)
objects for Python (supporting Python 3.3+).

This library lets you use a data structure with JSON reference objects,
as if the references had been replaced with the referent data.

```python console
>>> from pprint import pprint
>>> import jsonref

>>> # An example json document
>>> json_str = """{"real": [1, 2, 3, 4], "ref": {"$ref": "#/real"}}"""
>>> data = jsonref.loads(json_str)
>>> pprint(data)  # Reference is not evaluated until here
{'real': [1, 2, 3, 4], 'ref': [1, 2, 3, 4]}
```

# Features

-   References are evaluated lazily. Nothing is dereferenced until it is
    used.
-   Recursive references are supported, and create recursive python data
    structures.

References objects are actually replaced by lazy lookup proxy objects
which are almost completely transparent.

```python console
>>> data = jsonref.loads('{"real": [1, 2, 3, 4], "ref": {"$ref": "#/real"}}')
>>> # You can tell it is a proxy by using the type function
>>> type(data["real"]), type(data["ref"])
(<class 'list'>, <class 'jsonref.JsonRef'>)
>>> # You have direct access to the referent data with the __subject__
>>> # attribute
>>> type(data["ref"].__subject__)
<class 'list'>
>>> # If you need to get at the reference object
>>> data["ref"].__reference__
{'$ref': '#/real'}
>>> # Other than that you can use the proxy just like the underlying object
>>> ref = data["ref"]
>>> isinstance(ref, list)
True
>>> data["real"] == ref
True
>>> ref.append(5)
>>> del ref[0]
>>> # Actions on the reference affect the real data (if it is mutable)
>>> pprint(data)
{'real': [2, 3, 4, 5], 'ref': [2, 3, 4, 5]}
```
