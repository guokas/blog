---
layout: post
title: "Python JSON Example"
date: 2019-09-01 10:01:01+0800
tags:
  - Python
toc: true
toc_label: "Table of Contents"
toc_icon: "cog"
comments: true
description: Python JSON example, JSON is a lightweight data-interchange format.Python builds very powerful functionality support it.decording with load/loads, encoding with dump/dumps.
excerpt: Python JSON example, JSON is a lightweight data-interchange format.Python builds very powerful functionality support it.decording with load/loads, encoding with dump/dumps.
---
JSON (JavaScript Object Notation) is a lightweight data-interchange format. Python builds very powerful build-in functionality support JSON. This article will discuss the encoding and decoding of JSON in Python. make sure you have already known JSON syntax before starting if you aren't, please check it first.

# Python support JSON Natively!
Python has a build-in package json for encoding and decoding JSON data. just throw the below little guy up to your file in the first line.

```python
import json
```

# Decoding JSON
json library package provides two methods for deserializing from a string(json format) to a Python Object. 
```python
json.loads(str) #Decode the json string
json.load(fileobj) #Decode while JSON file read
```
`load()` accepted a file_object parameter, deserializing from a file contents. however `loads()` accepted string parameter, deserializing from a string.

if the string data or the file contents being deserialized is not a valid JSON document, then a `JSONDecodeError` will be raised.
```python
import json

# some JSON:
x =  '{ "school":"oxford","type":"university","location":"England"}'

# parse x:
y = json.loads(x)

# the result y is a Python dictionary:
res = y["school"] + " is a "+y["type"]+" in "+y["location"]

# Output: oxford is a university in England
print(res)
```

# Encoding JSON
json package also provides two methods for serializing Python Object as a JSON formatted string. they are:
```python
json.dumps() #encoding to JSON objects
json.dump() #encoded string writing on file
```
the `dump()` method will write the encoded string into a file. `dumps()` method produces only a JSON form string.

They both accept a `default` parameter that should be a function that gets called for objects that can't otherwise be serialized. it should return a JSON encoded version of the object or raise `TypeError`. 

`TypeError` will be raised, if not specified the `default` parameter while meeting unable to handled types.
```python
import json

# a Python object (dict):
x = {
    "name":"Jay gatsby",
    "gender":"male",
    "career":["thief","gambler","bootlegger"],
    "beloved":"daisy",
    "friends":"nick carraway",
    "great":"yes"
}

# convert into JSON:
# the result y is a JSON string:
y = json.dumps(x)

# Output: {"name": "Jay gatsby", "gender": "male", "career": ["thief", "gambler", "bootlegger"], "beloved": "daisy", "friends": "nick carraway", "great": "yes"}
print(y)
```

you have already know how to deserializing and serializing till here. in the next chapter, we will discuss the variable conversion table between JSON and Python.
# Conversion table
## JSON to Python

| JSON          | Python             |
|---------------|--------------------|
| Object        | dict               |
| Array         | list               |
| String        | unicode            |
| number – int  | number - int, long |
| number – real | float              |
| True          | True               |
| False         | False              |
| Null          | None               |

you should make sure JSON string is valid JSON form when converting a JSON string into python. the website can help you [jsonviwer](http://jsonviewer.stack.hu/).

## Python to JSON

| Python             | JSON          |
|--------------------|---------------|
| dict               | Object        |
| list               | Array         |
| unicode            | String        |
| number - int, long | number – int  |
| float              | number – real |
| True               | True          |
| False              | False         |
| None               | Null          |

when you convert Python into JSON form string, you should also guarantee that it's a valid python datatype that can be converted into JSON form. you should specify `default` parameter as a function that for custom conversion if you are not using above datatype sheet when converting.

# Conclusion
Now everything is very clear. I will summarize steps once more.
* import `json` package
* deserializing use `loads()`/`load()`
* serializing  use `dumps()`/`dump()`
* make all of the data that converting is valid

if you have some recommendations or thoughts, don't stingy your words.
write it in the comment area we will discuss it together.
