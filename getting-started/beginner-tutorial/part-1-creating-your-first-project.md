# Part 1: Creating Your First Project

## Introduction

Throughout this tutorial we will be building a task management application called `todo`.  We will begin with a barebones template using the `cement generate` tool, and build up from there to cover as much of the core features of the framework as possible.

Our application will include the following features:

* Ability to create, update, and delete task items
* List and display tasks via [Jinja2](../../extensions/jinja2.md) templates
* Persist data using [TinyDB](http://tinydb.readthedocs.io/en/latest/intro.html)
* Send an email message when tasks are complete

## Generating a New Project Repository

Cement includes a cli utility called `cement` that has tools to make developers lives easier.  This was introduced in Cement 3, and will continue to grow as feature requests are made for new and improved ways of streamlining the development process.

Create a new project with the following command and parameters:

```text
$ cement generate project ./todo
INFO: Generating cement project in ./todo
App Label [myapp]: todo
App Name [My Application]: My TODO Application
App Class Name [MyApp]: Todo
App Description [MyApp Does Amazing Things!]: A Simple TODO Application
Creator Name [John Doe]: Your Name
Creator Email [john.doe@example.com]: you@yourdomain.com
Project URL [https://github.com/johndoe/myapp/]: https://github.com/yourname/todo/
License [unlicensed]:
```

## Exploring The TODO Project

The following covers the primary components included with your new project.  First, take a look at the generated directory:

```text
.
├── CHANGELOG.md
├── Dockerfile
├── LICENSE.md
├── MANIFEST.in
├── Makefile
├── README.md
├── config
│   └── todo.yml.example
├── docs
├── requirements-dev.txt
├── requirements.txt
├── setup.cfg
├── setup.py
├── tests
│   ├── conftest.py
│   └── test_main.py
└── todo
    ├── __init__.py
    ├── controllers
    │   ├── __init__.py
    │   └── base.py
    ├── core
    │   ├── __init__.py
    │   ├── exc.py
    │   └── version.py
    ├── ext
    │   └── __init__.py
    ├── main.py
    ├── plugins
    │   └── __init__.py
    └── templates
        ├── __init__.py
        └── command1.jinja2

9 directories, 24 files
```

This looks like a lot, however there is a lot of placeholders here for best practice or recommended design.  Keep in mind, a Cement application can be as simple as a single file script... however we know that our TODO application will be disrupting the industry of task management, therefore we want to start things on the right track. 

Let's break this down into more manageable pieces:

**Common Project Files**

```text
├── CHANGELOG.md
├── LICENSE.md
├── README.md
```

These files should look familiar as they are common in most projects.  The `README.md` is populated with some starter info to help get more familiar with the layout and navigation of the project.  You should read the `README.md` now. 

**Common Python Packaging Files**

```text
├── MANIFEST.in
├── requirements-dev.txt
├── requirements.txt
├── setup.cfg
├── setup.py
```

These files should look familiar to anyone who has packaged and distributed a Python project before, and are required for proper installation, setup, and distribution.  Note that the `requirements.txt` lists dependencies that are strictly required for deployment \(production\), where the additional `requirements-dev.txt` includes additional dependencies that are only required for development \(running tests, building documentation, etc\).

**Miscellaneous Development**

```text
├── Dockerfile
├── Makefile
```

The included `Dockerfile` gives you a working Docker image out-of-the box, while the `Makefile` includes several helpers for common development tasks such as creating a virtualenv, running tests, building docker, etc.

**Default Configuration, Documentation, and Tests**

```text
├── config
│   └── todo.yml.example
├── docs
├── tests
│   ├── conftest.py
│   └── test_main.py
```

A good application has excellent documentation and testing, along with example configuration files of the applications settings \(and their defaults\).  As configuration defaults are added \(or modified\) in the application, the `config/todo.yml.example` should be updated to reflect them.

**Our Application Module**

```text
└── todo
    ├── __init__.py
    ├── controllers
    │   ├── __init__.py
    │   └── base.py
    ├── core
    │   ├── __init__.py
    │   ├── exc.py
    │   └── version.py
    ├── ext
    │   └── __init__.py
    ├── main.py
    ├── plugins
    │   └── __init__.py
    └── templates
        ├── __init__.py
        └── command1.jinja2
```

Finally, our code lives in a python module called `todo`, with what should be an obvious breakdown of submodules that clearly separate code into relevant and organized buckets.  Take a moment to briefly review all of the files provided in the generated project repository.

Again, note that a Cement project does not need to be organized this way but is a solid starting point to streamline development.  If you aren't entirely happy with the layout and organization of the generated projects you can always [create your own templates to start from](../developer-tools.md#customizing-templates).

## Setting up Our Development Environment

Our generated project includes a `Makefile` with several helpers to streamline the development process.  You can use these helpers, add your own, or do away with it and follow any other development process you are more familiar with.

First we will setup our VirtualENV:

```text
$ make virtualenv

$ source env/bin/activate

|> todo <| $ 
```

This runs common commands to create our virtual environment in `./env/`, and then runs `pip` to install our dependencies.  We activate the environment, and are ready to run our TODO application:

```text
|> todo <| $ todo --help
usage: todo [-h] [-d] [-q] [-v] [-l {info,warning,error,debug,fatal}]
            {command1} ...

A Simple TODO Application

optional arguments:
  -h, --help            show this help message and exit
  -d, --debug           full application debug mode
  -q, --quiet           suppress all console output
  -v, --version         show program's version number and exit
  -l {info,warning,error,debug,fatal}
                        logging level

sub-commands:
  {command1}
    command1            example sub command1

Usage: todo command1 --foo bar
```

Look at that!  Let's play around with some of the pre-built features:

```text
### display version information

$ todo --version
A Simple TODO Application 0.0.1.dev20180730022818
Cement Framework 2.99.2
Python 3.7.0
Platform Darwin-17.7.0-x86_64-i386-64bit

### display sub-command help

$ todo command1 --help
usage: todo command1 [-h] [-f FOO]

optional arguments:
  -h, --help         show this help message and exit
  -f FOO, --foo FOO  notorious foo option

### execute the example command1 sub-command

$ todo command1 -f bar
Example Template (templates/command1.jinja2)
Foo => bar
```

## Adding Functionality

Now that we have a working development environment and have become familiar with running our `todo` app, we can built in our initial feature set to manage tasks.

### Persistent Storage

Before we can create anything, we need some way to store it.  For this project, we've chosen [TinyDB](http://tinydb.readthedocs.io/en/latest/), a light-weight key-value database that stores data on disk in a single JSON file.

Let's begin by adding the dependency to our `requirements.txt` file, and installing it to our virtualenv:

{% tabs %}
{% tab title="Add TinyDB Dependency" %}
Add the following to the bottom of the pip `requirements.txt` file:

```text
tinydb
```

Install the new requirements with `pip`:

```text
$ pip install -r requirements.txt
...
Successfully installed tinydb-3.10.0
```
{% endtab %}
{% endtabs %}

With our dependency installed, we need to add it to our application.  Two primary things we will cover here are:

* Configuration settings for where we will store the `db.json` file on disk
* Extending our `app` with a `db` object we will use to integrate and access the TinyDB functionality in our application

{% tabs %}
{% tab title="Add Configuration Defaults" %}
Find and modify the following section of `todo/main.py` in order to define a default configuration for our database file called `db_file`:

```python
# configuration defaults
DEFAULTS = init_defaults('todo')
DEFAULTS['todo']['db_file'] = '~/.todo/db.json'
```

To be kind to our users, we will also want to add this default setting to our example configuration file `config/todo.yml.example`.  Modify the file to include the following:

```yaml
---
todo:

### Database file path
# db_file: ~/.todo/db.json
```
{% endtab %}
{% endtabs %}

{% tabs %}
{% tab title="Add DB Object Code" %}
We want to extend our application with a re-usable `db` object that can be used throughout or code.  There are many ways we could do this, however we are going to use a [framework hook](../../core-foundation/hooks.md).  Add the following to the top of the `todo/main.py` file:

```python
import os
from tinydb import TinyDB
from cement.utils import fs

def extend_tinydb(app):
    app.log.info('extending todo application with tinydb')
    db_file = app.config.get('todo', 'db_file')
    
    # ensure that we expand the full path
    db_file = fs.abspath(db_file)
    app.log.info('tinydb database file is: %s' % db_file)
    
    # ensure our parent directory exists
    db_dir = os.path.dirname(db_file)
    if not os.path.exists(db_dir):
        os.makedirs(db_dir)

    app.extend('db', TinyDB(db_file))
```

We've created a function to extend our application object to include an `app.db` object, however in order for it to take affect we need to register the function as a hook with the framework.  Add the following `hooks` meta option to our Todo app in `todo/main.py`:

```python
class Todo(App):
    class Meta:
        hooks = [
            ('post_setup', extend_tinydb),
        ]
```

Now, when we run `todo` again you will see that our hook is executed \(via the `info` logs\):

```text
$ todo --help
INFO: extending todo application with tinydb
INFO: tinydb database file is: /Users/derks/.todo/db.json
...
```

And we can see that the database was created:

```text
$ cat ~/.todo/db.json
{"_default": {}}
```
{% endtab %}
{% endtabs %}

