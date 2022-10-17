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
