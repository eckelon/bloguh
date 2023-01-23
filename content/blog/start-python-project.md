+++
title="How to start a Python project easily"
date="2023-01-10T09:00:00.000"
author=["JA Samitier"]
categories=["development"]
tags=["development", "software-engineering", "python", "work"]
description= "Learn how to easily start and maintain a python project with virtual environments and make"
toc=true
[featureimage]
    fileName = "start-python-project.jpg"
    alias = "person drafting on blueprint"
    creditAuthor = "Daniel McCullough "
    creditAuthorUrl = "https://unsplash.com/@d_mccullough"
    creditPlatform = "Unsplash"
    creditUrl = "https://unsplash.com&utm_medium=referral&utm_content=creditCopyText"
+++

I like to try out new technologies and programming languages, and one of the first blockers I face is how to start a project. I absolutely love the way you can start a NodeJS project with `npm init`. You can do something similar with Python.

In this article, you'll learn how to start a Python project assuring that its dependencies won't interfere with other projects' dependencies. And how to use Make to distribute your project to be easily installed in other development machines in no time.

## Check your versions

First, check your versions. I'm working with Python 3.10, but this will work with any version of Python 3.

```bash
λ python --version
Python 3.10.8
```

## Creating the virtual environment

First, create a folder to store your project.

```bash
mkdir ~/Development/python-starter-example
cd ~/Development/python-starter-example
```

Now, you need to create "the project" itself, aka the [virtual environment](https://docs.python.org/3/library/venv.html). The `venv` module allows you to have all the dependencies and configuration for your Python project inside the project directory, something like NodeJS projects having all the dependencies in `node_modules`. Let's do this.

```bash
cd ~/Development/python-starter-example
python -m venv env
```

## Working with the virtual environment

Now, the environment is created, but we need to go "inside", by activating it:

```bash
source ./env/bin/activate 
```

Every time you go back to the project directory, you'll need to activate it.

**How I know that I'm inside the environment?**

Easy-peasy: You'll see a little `(env)` text in the prompt of your terminal.

Now, every Python command you run inside the environment will be executed in that context, so, if you install a Python library, it won't interfere with other versions of that library from other python projects. 

## Installing dependencies

Let's install `Flask`, for example:

```bash
# (env) => we are inside the environment
pip install flask
```

The moment of truth: let's check that `Flask` was installed **inside** the environment.

```bash
~/Development/python-starter-example
env λ ls env/lib/python3.10/site-packages/flask 
__init__.py	app.py		config.py	globals.py	logging.py	sessions.py	testing.py	wrappers.py
__main__.py	blueprints.py	ctx.py		helpers.py	py.typed	signals.py	typing.py
__pycache__	cli.py		debughelpers.py	json		scaffold.py	templating.py	views.py
```

So it's there! But.. what about the Flask binary? Remember that some Python libraries come with a binary you need to execute sometimes. Those binaries will be under the `env/bin` folder inside your project. See:

```bash
~/Development/python-starter-example
env λ ls env/bin                               
Activate.ps1	activate.csh	flask		pip3		python		python3.10
activate	activate.fish	pip		pip3.10		python3
```

## Running the project

Let's use the example `Flask` application [from its documentation](https://flask.palletsprojects.com/en/2.2.x/quickstart/). Create a file called `app.py` in the project and edit it:

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello_world():
    return "<p>Hello, World!</p>"
```

Now, run it. Remember that the project need to use the `Flask` version under the environment:

```bash
env/bin/flask --app app run  

* Serving Flask app 'app'
 * Debug mode: off
WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
 * Running on http://127.0.0.1:5000
Press CTRL+C to quit
127.0.0.1 - - [22/Jan/2023 13:12:24] "GET / HTTP/1.1" 200 -
127.0.0.1 - - [22/Jan/2023 13:12:24] "GET /favicon.ico HTTP/1.1" 404 -
```

et voilà!

You could also run it directly using the `python` command. You just need to tweak the code a little.

```python
import os
from flask import Flask

app = Flask(__name__)


@app.route("/")
def hello_world():
    return "<p>Hello, World!</p>"


if __name__ == '__main__':
    port = int(os.environ.get('PORT', 3000))
    app.run(host='127.0.0.1', port=port)
```

Now you can run it with the `python` command:

```bash
~/Development/python-starter-example 10s
env λ python app.py
 * Serving Flask app 'app'
 * Debug mode: off
WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
 * Running on http://127.0.0.1:3000
Press CTRL+C to quit
127.0.0.1 - - [22/Jan/2023 13:16:37] "GET / HTTP/1.1" 200 -
127.0.0.1 - - [22/Jan/2023 13:16:38] "GET /favicon.ico HTTP/1.1" 404 -
```

## Create the requirements file

If you want to distribute your project with the rest of your team, or in a git repository, you need to create [the `requirements.txt` file that will contain all the dependencies that your project's using](https://pip.pypa.io/en/latest/user_guide/#requirements-files). Since you installed everything using `pip`, the easiest way of creating this file is using the `pip freeze` command.


```bash
pip freeze > requirements.txt
```

Now you have your requirements file - something like this:

```bash
~/Development/python-starter-example
env λ cat requirements.txt            
click==8.1.3
Flask==2.2.2
itsdangerous==2.1.2
Jinja2==3.1.2
MarkupSafe==2.1.2
Werkzeug==2.2.2
```

Now that the dependencies are documented in the `requirements.txt` file, anyone can install them with a simple `pip` command:

```bash
pip install -r requirements.txt
```

## Creating an application runner with Make

Now, let's automatize it. What if we could create a way of automatically create the virtual env with everything installed in it? I like doing this with `Make`. Create a file called `Makefile` inside the project directory and start with this:

```makefile
.PHONY: all

env/bin/activate: requirements.txt
	python -m venv env
	./env/bin/pip install -r requirements.txt

run: env/bin/activate
	./env/bin/python app.py

freeze: env/bin/pip
	./env/bin/pip freeze > requirements.txt

clean:
	rm -rf __pycache__
	rm -rf ./env
```

Let's break down this `Makefile`. It has three commands:

- `run`: will run the `app.py` Python file if the `venv` is active. If it's not it will create the virtual environment and install its dependencies. If no `requirements.txt` file is found, then nothing will happen.
- `freeze`: will update the `requirements.txt` with all the libraries installed with `pip`. This is useful if you installed new libraries.
- `clean`: will delete the Python cache and the environment. You can use this safely, because if you run your application again, everything will be re-created!

Using `Make` is really easy, just type `make` and the command you want to execute, e.g:

```bash
make run
```

*Please, not that you need to have make installed. In a mac you can do it with `brew` (`brew install make`), in Linux is usually installed or available in the software repositories (`apt`, `dnf`...), and in Windows you can use [WSL to install a Linux distribution inside Windows](https://learn.microsoft.com/en-us/windows/wsl/about) and run make from there.*

## Let's sum up

In this article, you learned how to create a Python project in its own virtual environment, and also how to create a Makefile to run everything from it. Now, when someone clones that project, they only will need to type `make run` and it automatically creates the environment, install the dependencies and run the project. Yay!

I hope you found this interesting. Of course this isn't the only way to manage a Python project. This is the way I use to do it. If there's something I missed, please, ping me, and I'll update the article. Thanks!



