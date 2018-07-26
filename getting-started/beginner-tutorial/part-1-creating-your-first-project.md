# Part 1: Creating Your First Project

## Introduction

Throughout this tutorial we will be building a TODO application called `todo`.  We will begin with a barebones template using the `cement generate` tool, and build up from there to cover as much of the core features of the framework as possible.

Our todo application will include the following features:

* Ability to create, update, and delete task items
* List and display tasks via Jinja2 templates
* Persist data using [TinyDB](http://tinydb.readthedocs.io/en/latest/intro.html)
* Send an email message when tasks are complete

## Generating a New Project Repository

Cement includes a cli utility called `cement` that has tools to make developers lives easy.  This was introduced in Cement 3, and will continue to grow as feature requests are made for new and improved ways of streamlining the development process.

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
todo/
    config/
    docker/
    tests/
    todo/
        controllers/
        core/
        ext/
        plugins/
        templates/
        bootstrap.py
        main.py
    CHANGELOG.md
    Dockerfile
    LICENSE.md
    MANIFEST.in
    Makefile
    README.md
    docker-compose.yml
    requirements-dev.txt
    requirements.txt
    setup.cfg
    setup.py
```



## 

