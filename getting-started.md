# Getting Started

{% hint style="info" %}
The developer guide assumes intermediate level knowledge of Python. If you are totally new to Python development, you might want to get more familiar with the language before jumping into a framework.
{% endhint %}

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

### Obligatory Hello World

{% tabs %}
{% tab title="helloworld.py" %}
```python
from cement import App

with App('helloworld') as app:
    app.run()
    app.log.info('Hello World!')
```
{% endtab %}

{% tab title="Usage" %}
```text
$ python helloworld.py --help
usage: helloworld [-h] [--debug] [--quiet]

optional arguments:
  -h, --help  show this help message and exit
  --debug     toggle debug output
  --quiet     suppress all output

$ python helloworld.py
INFO: Hello World!
```
{% endtab %}
{% endtabs %}

### Cement Developer Tools CLI

Cement ships with a CLI utility that includes tools and helpers for application developement. It is in-itself an application Built on Cement™, and can serve as a working example of some of the key features of the framework.

See: `$ cement --help`

The Cement CLI uses the builtin `Generate Extension` in order to easily create new applications, extensions, plugins, or scripts.

See `$ cement generate --help`

### Creating Your First Application Built on Cement™

Using the Cement Developer Tools CLI, you can quickly generate a new application:

_Note: Press_ `<ENTER>` _in order to take the default values for_ `myapp`_:_

```text
$ cement generate app ./myapp
INFO: Generating cement app in ./myapp
App Label [myapp]:
App Name [My Application]:
App Class Name [MyApp]:
App Description [MyApp Does Amazing Things!]:
Creator Name [John Doe]:
Creator Email [john.doe@example.com]:
Project URL [https://github.com/johndoe/myapp/]:
License [unlicensed]:
```

We have just generated a new application in the directory `./myapp`.

