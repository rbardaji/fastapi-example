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

## Data Handling With pydantic

All the data validation is performed under the hood by pydantic, so you get all the benefits from it, and you know you are in good hands.

You can use the same type declarations with str, float, bool and many other complex data types.

