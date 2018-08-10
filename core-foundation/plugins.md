# Application Plugins

## Introduction to the Plugin Interface

Cement defines a Plugin Interface, as well as the default [CementPluginHandler](https://cement.readthedocs.io/en/2.99/api/ext/ext_plugin/#cement.ext.ext_plugin.CementPluginHandler) that implements the interface.

{% hint style="warning" %}
Cement often includes multiple handler implementations of an interface that may or may not have additional features or functionality than the interface requires.  The documentation below only references usage based on the interface and default handler \(not the full capabilities of an implementation\).
{% endhint %}

**Cement Extensions that Provide Plugin Handlers:**

* [Plugin](../extensions/plugin.md)

**API References:**

* [Cement Core Plugin Module](https://cement.readthedocs.io/en/2.99/api/core/plugin/)

## Configuration

### Application Configuration Settings

The following settings under the applications primary configuration section modify plugin handling:

| **Setting** | **Description** |
| :--- | :--- |
| **plugin\_config\_dir** | A directory path where plugin config files can be found.  Will be **appended** to `App.Meta.plugin_config_dirs` |
| **plugin\_dir** | A directory path where plugin code can be found.  Will be **prepended** to `App.Meta.plugin_dirs` |

### Application Meta Options:

The following options under [`App.Meta`](https://cement.readthedocs.io/en/2.99/api/core/foundation/#cement.core.foundation.App.Meta) modify plugin handling:

| **Option** | **Description** |
| :--- | :--- |
| **plugins** | A list of plugins to load.  This is generally considered bad practice since plugins should be dynamically enabled/disabled via the application's configuration files. Default: `[]` |
| **plugin\_config\_dirs** | A list of directory paths where plugin config files can be found.  Files must end in `.conf` \(or whatever is defined by `App.Meta.config_file_suffix`\).  Default: `['/etc/myapp/plugins.d', '~/.config/myapp/plugins.d', '~/.myapp/plugins.d']` |
| **plugin\_bootstrap** | A python module \(dotted import path\) where plugin code can be loaded from instead of external directories.  This is generally something like `myapp.plugins` \(builtin plugins shipped with the app code\)..  Default: `myapp.plugins` |
| **plugin\_dirs** | A list of directory paths where plugin code \(modules\) can be loaded from \(external to the application\).  Default: `['~/.myapp/plugins/', '/usr/lib/myapp/plugins']` |

## Creating a Plugins

The plugin system is a mechanism for dynamically loading code to extend the functionality of a specific application. In general, this includes the registration of interfaces, handlers, and/or hooks but can include controllers, command-line options, or anything else.

The preferred method of creating an plugin would be via the included [developer tools](../getting-started/developer-tools.md):

```text
$ cement generate plugin /path/to/myapp/plugins
```

This will produce an example plugin directory like the following:

```text
├── __init__.py
├── controllers
│   ├── __init__.py
│   └── myplugin.py
└── templates
    ├── __init__.py
    └── plugins
        └── myplugin
            └── command1.jinja2
```

The example plugin includes a controller, sub-command, and output generated via Jinja2 template.  That said, the only thing Cement needs is a `load()` function.... everything else is arbitrary.  In the generated plugin, we find that in `myplugin/__init__.py`:

```python
import os
from .controllers.myplugin import MyPlugin

def add_template_dir(app):
    path = os.path.join(os.path.dirname(__file__), 'templates')
    app.add_template_dir(path)

def load(app):
    app.handler.register(MyPlugin)
    app.hook.register('post_setup', add_template_dir)
```

{% hint style="info" %}
Plugins can provide anything from defining interfaces, registering hooks, or even adding command line arguments.  The only thing required to make up a plugin is a `load()` function in `myplugin.py` or `myplugin/__init__.py`.
{% endhint %}

You will notice that plugins are essentially the same as framework extensions, however the difference is both when/how the code is loaded, as well as the purpose of that code. 

{% hint style="info" %}
Framework extensions add functionality **to the framework** for the application to utilize, where application plugins **extend the functionality of the application** itself.
{% endhint %}

## Loading a Plugin

Plugin modules are looked for first in one of the defined `plugin_dirs`, and if not found then Cement attempts to load them from the `plugin_bootstrap`. The following application shows how to configure an application to load plugins. Take note that these are the **default settings** and will work the same if not defined:

```text
from cement.core.foundation import CementAppfrom cement.core.controller import CementBaseController, expose​class MyBaseController(CementBaseController):    class Meta:        label = 'base'        description = 'MyApp Does Amazing Things'​class MyApp(CementApp):    class Meta:        label = 'myapp'        base_controller = MyBaseController        plugin_bootstrap='myapp.bootstrap',        plugin_config_dirs=[            '/etc/myapp/plugins.d',            '~/.myapp/plugins.d',            ]        plugin_dirs=[            '/usr/lib/myapp/plugins',            '~/.myapp/plugins',            ]​​def main():    with MyApp() as app:        app.run()​if __name__ == '__main__':    main()
```

We modified the default settings for `plugin_config_dirs` and `plugin_dirs`. These are the default settings under `Cementapp`, however we have put them here for clarity.

Running this application will do nothing particularly special, however the following demonstrates what happens when we add a simple plugin that provides an application controller:

**/etc/myapp/plugins.d/myplugin.conf**

```text
[myplugin]enable_plugin = truesome_option = some value
```

**/usr/lib/myapp/plugins/myplugin.py**

```text
from cement.core.controller import CementBaseController, exposefrom cement.utils.misc import init_defaults​defaults = init_defaults('myplugin')​class MyPluginController(CementBaseController):    class Meta:        label = 'myplugin'        description = 'this is my plugin description'        stacked_on = 'base'        config_defaults = defaults        arguments = [            (['--some-option'], dict(action='store')),            ]​    @expose(help="this is my command description")    def my_plugin_command(self):        print 'In MyPlugin.my_plugin_command()'​def load(app):    app.handler.register(MyPluginController)
```

Running our application with the plugin disabled, we see:

```text
$ python myapp.py --helpusage: myapp.py (sub-commands ...) [options ...] {arguments ...}​MyApp Does Amazing Things​optional arguments:  -h, --help  show this help message and exit  --debug     toggle debug output  --quiet     suppress all output
```

But if we enable the plugin, we get something a little different:

```text
$ python myapp.py --helpusage: myapp.py (sub-commands ...) [options ...] {arguments ...}​MyApp Does Amazing Things​commands:​  my-plugin-command    this is my command description​optional arguments:  -h, --help            show this help message and exit  --debug               toggle debug output  --quiet               suppress all output  --some-option SOME_OPTION
```

We can see that the `my-plugin-command` and the `--some-option` option were provided by our plugin, which has been 'stacked' on top of the base controller.

## User Defined Plugin Configuration and Module Directories

Most applications will want to provide the ability for the end-user to define where plugin configurations and modules live. This is possible by setting the `plugin_config_dir` and `plugin_dir` settings in any of the applications configuration files. Note that these paths will be **added** to the built-in `plugin_config_dirs` and `plugin_dirs` settings respectively, rather than completely overwriting them. Therefore, your application can maintain it's default list of plugin configuration and module paths while also allowing users to define their own.

**/etc/myapp/myapp.conf**

```text
[myapp]plugin_dir = /usr/lib/myapp/pluginsplugin_config_dir = /etc/myapp/plugins.d
```

The `plugin_bootstrap` setting is however only configurable within the application itself.

## What Can Go Into a Plugin?

The above example shows how to add an optional application controller via a plugin, however a plugin can contain anything you want. This could be as simple as adding a hook that does something magical. For example:

```text
from cement.core import hook​def my_magical_hook(app):    # do something magical    print('Something Magical is Happening!')​def load(app):    hook.register('post_setup', my_magical_hook)
```

And with the plugin enabled, we get this when we run the same app defined above:

```text
$ python myapp.pySomething Magical is Happening!
```

The primary detail is that Cement calls the `load()` function of a plugin... after that, you can do anything you like.

## Single File Plugins vs. Plugin Directories

As of Cement 2.9.x, plugins can be either a single file \(i.e `myplugin.py`\) or a python module directory \(i.e. `myplugin/__init__.py`\). Both will be loaded and executed the exact same way.

One caveat however, is that the submodules referenced from within a plugin directory must be relative path. For example:

**myplugin/init.py**

```text
from .controllers import MyPluginController​def load(app):    app.handler.register(MyPluginController)
```

**myplugin/controllers.py**

```text
from cement.core.controller import CementBaseController, expose​class MyPluginController(CementBaseController):    class Meta:        label = 'myplugin'        stacked_on = 'base'        stacked_type = 'embedded'​    @expose()    def my_command(self):        print('Inside MyPluginController.my_command()')
```

## Loading Templates From Plugin Directories

A common use case for complex applications is to use an output handler the uses templates, such as Mustache, Genshi, Jinja2, etc. In order for a plugin to use it's own template files it's templates directory first needs to be added to the list of template directories to be parsed. In the future, this will be more streamlined however currently the following is the recommeded way:

**myplugin/init.py**

```text
def add_template_dir(app):    path = os.path.join(os.path.basename(self.__file__, 'templates')    app.add_template_dir(path)​def load(app):    app.hook.register('post_setup', add_template_dir)
```

The above will append the directory `/path/to/myplugin/templates` to the list of template directories that the applications output handler with search for template files.

