# Application Framework for Python

### What is Cement?

Cement is an advanced Application Framework for Python, with a primary focus on Command Line Interfaces \(CLI\). Its goal is to introduce a standard, and feature-full platform for both simple and complex command line applications as well as support rapid development needs without sacrificing quality. Cement is flexible, and it’s use cases span from the simplicity of a micro-framework to the complexity of a mega-framework. Whether it’s a single file script, or a multi-tier application, Cement is the foundation you’ve been looking for.

The first commit to Git was on Dec 4, 2009. Since then, the framework has seen several iterations in design, and has continued to grow and improve since it’s inception. Cement is the most stable, and complete framework for command line and backend application development.

### Core Features

Core features include \(but are not limited to\):

* Core pieces of the framework are customizable via handlers/interfaces
* Extension handler interface to easily extend framework functionality
* Config handler supports parsing multiple config files into one config
* Argument handler parses command line arguments and merges with config
* Log handler supports console and file logging
* Plugin handler provides an interface to easily extend your application
* Hook support adds a bit of magic to apps and also ties into framework
* Handler system connects implementation classes with Interfaces
* Output handler interface renders return dictionaries to console
* Cache handler interface adds caching support for improved performance
* Controller handler supports sub-commands, and nested controllers
* Zero external dependencies\* of the core library
* 100% test coverage using `nose` and `coverage`
* 100% PEP8 and style compliant using `flake8`
* Extensive documentation
* Tested on Python 2.6, 2.7, 3.3, 3.4, 3.5

Note that argparse is required as an external dependency for Python &lt; 2.7 and &lt; 3.2. Additionally, some optional extensions that are shipped with the mainline Cement sources do require external dependencies. It is the responsibility of the application developer to include these dependencies along with their application if they intend to use any optional extensions that have external dependencies, as Cement explicitly does not include them.

### License

The Cement Framework is Open Source and is distributed under the BSD License \(three clause\). 

```text
Copyright (c) 2009-2017 Data Folk Labs, LLC
All rights reserved.
```

### Projects Built on Cement™

The following is an incomplete lists of notable projects that are Built on Cement™:

* [Amazon Elastic Beanstalk CLI](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb-cli3.html) \([PYPI](https://pypi.python.org/pypi/awsebcli)\)
* [Easy Engine](https://easyengine.io/) \([GitHub](https://github.com/EasyEngine/easyengine)\)
* [SentientHome](https://github.com/fxstein/SentientHome)
* [Pubkey](https://github.com/fxstein/pubkey)
* [HCE Project](http://hce-project.com/)
* [QLDS Manager](https://qlds-manager.readthedocs.io/en/stable/index.html) \([GitHub](https://github.com/rzeka/QLDS-Manager)\)

If you are building a project on the Cement Framework and would like to see your company or project listed here [please create an issue and/or pull request on GitHub](https://github.com/datafolklabs/cement/).

### Installation

Stable versions are available via PyPi:

```text
$ pip install cement
```

Development versions are available via Github:

```text
$ git clone git://github.com/datafolklabs/cement.git
$ cd cement/
$ python setup.py install
```

### Getting Started

The developer guide assumes intermediate level knowledge of Python. If you are totally new to Python development, you might want to get more familiar with the language before jumping into a framework.

Ex: Hello World:

{% code-tabs %}
{% code-tabs-item title="helloworld.py" %}
```python
from cement.core.foundation import CementApp

with CementApp('myapp') as app:    
    app.run()    
    print('Hello World!')
```
{% endcode-tabs-item %}
{% endcode-tabs %}

CLI Usage:

```text
$ python myapp.py --help
usage: myapp [-h] [--debug] [--quiet]

optional arguments:  
-h, --help  show this help message and exit  
--debug     toggle debug output  
--quiet     suppress all output

$ python myapp.py
Hello World!
```



