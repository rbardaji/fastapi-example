# Using FastAPI to Build Python Web APIs

Original documentation [here](https://realpython.com/fastapi-python-web-apis/).

Creating APIs, or **application programming interfaces**, is an important part of making your software accessible to a broad range of users. In this tutorial, you will learn the main concepts of **FastAPI** and how to use it to quickly create web APIs that implement best practices by default.

By the end of it, you will be able to start creating production-ready web APIs, and you will have the understanding needed to go deeper and learn more for your specific use cases.

**In this tutorial, you’ll learn how to:**

* Use **path parameters** to get a unique URL path per item
* Receive JSON data in your requests using **pydantic**
* Use API best practices, including *validation*, *serialization*, and *documentation*
* Continue learning about FastAPI for *your use cases*

This tutorial is written by the [author of FastAPI](https://realpython.com/team/sramirez/). It contains a careful selection of fragments from the official documentation, avoiding getting lost in technical details while helping you get up to speed as fast as possible.

To get the most out of this tutorial, it would be helpful for you to know the basics of [what HTTP is and how it works](https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview), [what JSON is](https://fastapi.tiangolo.com/python-types/), and Python type hints. You will also benefit from using a [virtual environment](https://realpython.com/python-virtual-environments-a-primer/), as is the case for any Python project.

## What Is FastAPI?

FastAPI is a modern, high-performance web framework for building APIs with Python based on standard type hints. It has the following key features:

* **Fast to run**: It offers very high performance, on par with **NodeJS** and **Go**, thanks to Starlette and pydantic.
* **Fast to code**: It allows for significant increases in development speed.
* **Reduced number of bugs**: It reduces the possibility for human-induced errors.
* **Intuitive**: It offers great editor support, with completion everywhere and less time debugging.
* **Straightforward**: It’s designed to be uncomplicated to use and learn, so you can spend less time reading documentation.
* **Short**: It minimizes code duplication.
* **Robust**: It provides production-ready code with automatic interactive documentation.
* **Standards-based**: It’s based on the open standards for APIs, OpenAPI and JSON Schema.

The framework is designed to optimize your developer experience so that you can write simple code to build production-ready APIs with best practices by default.

## Install FastAPI

As with any other Python project, it would be best to start by creating a virtual environment. If you are not familiar with how to do that, then you can check out the [Primer on Virtual Environments](https://realpython.com/python-virtual-environments-a-primer/).

The first step is to install FastAPI and [Uvicorn](https://www.uvicorn.org/#introduction) using [pip](https://realpython.com/what-is-pip/):

```cmd
python -m pip install fastapi uvicorn[standard]
```

With that, you have FastAPI and Uvicorn installed and are ready to learn how to use them. FastAPI is the framework you’ll use to build your API, and Uvicorn is the server that will use the API you build to serve requests.

## First Steps

To get started, in this section, you will create a minimal FastAPI app, run it with a server using Uvicorn, and then learn all the interacting parts. This will give you a very quick overview of how everything works.

### Create a First API

A basic FastAPI file looks like this:

```python
# main.py

from fastapi import FastAPI

app = FastAPI()

@app.get("/")
async def root():
    return {"message": "Hello World"}
```

Copy the code above to a file named main.py, and just like that, you have a fully functional API application with some best practices like automatic documentation and serialization built in. You will learn more about those features next.

This code defines your application, but it won’t run on itself if you call it with python directly. To run it, you need a server program. In the steps above, you already installed Uvicorn. That will be your server.

## Run the First API App With Uvicorn

Run the live server using Uvicorn:

```shell
$ uvicorn main:app --reload

INFO:     Uvicorn running on http://127.0.0.1:8000 (Press CTRL+C to quit)
INFO:     Started reloader process [28720]
INFO:     Started server process [28722]
INFO:     Waiting for application startup.
INFO:     Application startup complete.
```

The first info line in the output shows the URL where your app is being served in your local machine. Since you used --reload for development, when you update your application code, the server will reload automatically.

## Check the Response

Open your browser to http://127.0.0.1:8000, which will make your browser send a request to your application. It will then send a JSON response with the following:

```json
{"message": "Hello World"}
```

That JSON message is the same dictionary that you returned from the function in your application. FastAPI takes care of serializing the Python dict into a JSON object and setting the appropriate Content-Type.

## Check the Interactive API Documentation

Now open http://127.0.0.1:8000/docs in your browser.

You will see the automatic interactive API documentation provided by Swagger UI.

The browser-based user interface documenting your API is provided and integrated by default. You don’t have to do anything else to take advantage of it with FastAPI.

## Check the Alternative Interactive API Documentation

Now, go to http://127.0.0.1:8000/redoc in your browser.

You’ll see the alternative automatic documentation provided by ReDoc.

As FastAPI is based on standards like OpenAPI, there are many alternative ways to show the API documentation. FastAPI provides these two alternatives by default.

## Path Parameters: Get an Item by ID

You can declare path parameters or variables with the same syntax used by Python [formatted strings](https://realpython.com/python-formatted-output/):

```python
# main.py

from fastapi import FastAPI

app = FastAPI()

@app.get("/items/{item_id}")
async def read_item(item_id):
    return {"item_id": item_id}
```

The value of the path parameter item_id will be passed to your function as the argument item_id.

So, if you run this example and go to http://127.0.0.1:8000/items/foo, you will see this response:

```json
{"item_id":"foo"}
```

The response contains "foo", which is what was passed in the item_id path parameter and then returned in a dictionary.

### Path Parameters With Types

You can declare the type of a path parameter in the function using standard Python type hints:

```python
# main.py

from fastapi import FastAPI

app = FastAPI()

@app.get("/items/{item_id}")
async def read_item(item_id: int):
    return {"item_id": item_id}
```

In this case, you declare item_id to be an int.

Declaring the type of a path parameter will give you editor support inside of your function, with error checks, completion, and so on.

### Data Conversion

If you run the above example and navigate your browser to http://127.0.0.1:8000/items/3, then you will see the following response:

```json
{"item_id":3}
```

Notice that the value your function received and then returned is 3, which is a Python int, not a string ("3"). So, with that type declaration, FastAPI gives you automatic **request parsing**.

### Data Validation

If you point your browser to http://127.0.0.1:8000/items/foo, then you’ll see a nice HTTP error:

```json
{
    "detail": [
        {
            "loc": [
                "path",
                "item_id"
            ],
            "msg": "value is not a valid integer",
            "type": "type_error.integer"
        }
    ]
}
```

This is because the path parameter item_id has a value of "foo", which is not an int.

The same error would appear if you provided a float instead of an int, such as if you opened http://127.0.0.1:8000/items/4.2 in your browser. So, with the same Python type hint, FastAPI gives you both **data parsing** and **data validation**.

Also notice that the error clearly states the exact point where the validation didn’t pass. This is incredibly helpful while developing and debugging code that interacts with your API.

### Data Handling With pydantic

All the data validation is performed under the hood by pydantic, so you get all the benefits from it, and you know you are in good hands.

You can use the same type declarations with str, float, bool and many other complex data types.

### Order Matters: Put Fixed Paths First

When creating path operations, you may find situations where you have a fixed path, like /users/me. Let’s say that it’s to get data about the current user. You might also have the path /users/{user_id} to get data about a specific user by some user ID.

Because path operations are evaluated in order, you need to make sure that the path for /users/me is declared before the one for /users/{user_id}:

```python
# main.py

from fastapi import FastAPI

app = FastAPI()

@app.get("/users/me")
async def read_user_me():
    return {"user_id": "the current user"}

@app.get("/users/{user_id}")
async def read_user(user_id: str):
    return {"user_id": user_id}
```

Otherwise, the path for /users/{user_id} would also match for /users/me, thinking that it’s receiving the parameter user_id with a value of "me".

## Request Body: Receiving JSON Data

When you need to send data from a client to your API, you send it as a request body.

A **request body** is data sent by the client to your API. A **response body** is the data your API sends to the client. Your API almost always has to send a response body. But clients don’t necessarily need to send request bodies all the time.

> ***NOTE:*** To send data, you should use POST (the most common approach), PUT, DELETE, or PATCH. Sending a body with a GET request has undefined behavior in the specifications. Nevertheless, using a GET request is supported by FastAPI, though only for very complex or extreme use cases. As it is discouraged, the interactive documentation with Swagger UI won’t show the documentation for the body when using GET, and proxies in the middle might not support it.

To declare a request body, you use pydantic models, with all their power and benefits. You’ll learn more about them below.

### Use pydantic to Declare JSON Data Models (Data Shapes)

First, you need to import BaseModel from pydantic and then use it to create subclasses defining the **schema**, or data shapes, you want to receive.

Next, you declare your data model as a class that inherits from BaseModel, using standard Python types for all the attributes:

```python
# main.py

from typing import Optional

from fastapi import FastAPI
from pydantic import BaseModel

class Item(BaseModel):
    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None

app = FastAPI()

@app.post("/items/")
async def create_item(item: Item):
    return item
```

When a model attribute has a default value, it is not required. Otherwise, it is required. To make an attribute optional, you can use [None](https://realpython.com/null-in-python/).

For example, the model above declares a JSON object (or Python dict) like this:

```json
{
    "name": "Foo",
    "description": "An optional description",
    "price": 45.2,
    "tax": 3.5
}
```

In this case, since description and tax are optional because they have a default value of None, this JSON object would also be valid:

```json
{
    "name": "Foo",
    "price": 45.2
}
```

A JSON object that omits the default values is also valid.

Next, add the new pydantic model to your path operation as a parameter. You declare it the same way you declared path parameters:

```python
# main.py

from typing import Optional

from fastapi import FastAPI
from pydantic import BaseModel

class Item(BaseModel):
    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None

app = FastAPI()

@app.post("/items/")
async def create_item(item: Item):
    return item

```

The parameter ```item``` has a type hint of ```Item```, which means that item is declared as an instance of the class ```Item```.

With that Python type declaration, FastAPI will:

* Read the body of the request as JSON
* Convert the corresponding types if needed
* Validate the data and return a clear error if it is invalid
* Give you the received data in the parameter item—since you declared it to be of type Item, you will also have all the editor support, with completion and type checks for all the attributes and their types
* Generate [JSON Schema](https://json-schema.org/) definitions for your model that you can also use anywhere else that makes sense for your project

By using standard type hints with pydantic, FastAPI helps you build APIs that have all these best practices by default, with little effort.

### Use the pydantic Model

Inside the function, you can access all the attributes of the model object directly:

```python
# main.py

from typing import Optional

from fastapi import FastAPI
from pydantic import BaseModel

class Item(BaseModel):
    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None

app = FastAPI()

@app.post("/items/")
async def create_item(item: Item):
    item_dict = item.dict()
    if item.tax:
        price_with_tax = item.price + item.tax
        item_dict.update({"price_with_tax": price_with_tax})
    return item_dict
```

The parameter item is declared as an instance of the class Item, and FastAPI will make sure that you receive exactly that in your function instead of a dictionary or something else.

### Request Body and Path Parameters

You can declare path parameters and a request body at the same time.

FastAPI will recognize that the function parameters that match path parameters should be taken from the path and that function parameters that are declared to be pydantic models should be taken from the request body:

```python
# main.py

from typing import Optional

from fastapi import FastAPI
from pydantic import BaseModel

class Item(BaseModel):
    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None

app = FastAPI()

@app.put("/items/{item_id}")
async def create_item(item_id: int, item: Item):
    return {"item_id": item_id, **item.dict()}
```

This way, you can declare path parameters and JSON request bodies, and FastAPI will take care of doing all the data validation, serialization, and documentation for you. You could verify it by going to the same API documentation at /docs or by using other tools like [Postman](https://www.postman.com/) with a graphical interface or [Curl](https://curl.se/docs/httpscripting.html) in the command line.

In a similar way, you can declare more complex request bodies, like lists, and other types of request data, like query parameters, cookies, headers, form inputs, files, and so on.

## Query Parameters¶

When you declare other function parameters that are not part of the path parameters, they are automatically interpreted as "query" parameters.

```python
from fastapi import FastAPI

app = FastAPI()

fake_items_db = [{"item_name": "Foo"}, {"item_name": "Bar"}, {"item_name": "Baz"}]


@app.get("/items/")
async def read_item(skip: int = 0, limit: int = 10):
    return fake_items_db[skip : skip + limit]
```
The query is the set of key-value pairs that go after the ```?``` in a URL, separated by ```&``` characters.

For example, in the URL:

```shell
http://127.0.0.1:8000/items/?skip=0&limit=10
```

...the query parameters are:

* ```skip```: with a value of ```0```
* ```limit```: with a value of ```10```

As they are part of the URL, they are "naturally" strings.

But when you declare them with Python types (in the example above, as ```int```), they are converted to that type and validated against it.

All the same process that applied for path parameters also applies for query parameters:

* Editor support (obviously)
* Data "parsing"
* Data validation
* Automatic documentation

### Defaults

As query parameters are not a fixed part of a path, they can be optional and can have default values.

In the example above they have default values of skip=0 and limit=10.

So, going to the URL:

```shell
http://127.0.0.1:8000/items/
```

would be the same as going to:

```shell
http://127.0.0.1:8000/items/?skip=0&limit=10
```

But if you go to, for example:

```shell
http://127.0.0.1:8000/items/?skip=20
```

The parameter values in your function will be:

skip=20: because you set it in the URL
limit=10: because that was the default value

### Optional parameters¶

The same way, you can declare optional query parameters, by setting their default to ```None```:


```pyhton
from typing import Union

from fastapi import FastAPI

app = FastAPI()


@app.get("/items/{item_id}")
async def read_item(item_id: str, q: Union[str, None] = None):
    if q:
        return {"item_id": item_id, "q": q}
    return {"item_id": item_id}
```

In this case, the function parameter ```q``` will be optional, and will be ```None``` by default.

### Query parameter type conversion

You can also declare ```bool``` types, and they will be converted:

```python
from typing import Union

from fastapi import FastAPI

app = FastAPI()


@app.get("/items/{item_id}")
async def read_item(item_id: str, q: Union[str, None] = None, short: bool = False):
    item = {"item_id": item_id}
    if q:
        item.update({"q": q})
    if not short:
        item.update(
            {"description": "This is an amazing item that has a long description"}
        )
    return item
```

In this case, if you go to:

```shell
http://127.0.0.1:8000/items/foo?short=1
```

or

```shell
http://127.0.0.1:8000/items/foo?short=True
```

or

```
http://127.0.0.1:8000/items/foo?short=true
```

or

```
http://127.0.0.1:8000/items/foo?short=on
```

or

```shell
http://127.0.0.1:8000/items/foo?short=yes
```

or any other case variation (uppercase, first letter in uppercase, etc), your function will see the parameter short with a ```bool``` value of ```True```. Otherwise as ```False```.