# Framework Extensions

Cement defines an extension interface called [IExtension](https://docs.builtoncement.com/%7B%7B%20version%20%7D%7D/api/core/extension.html#cement.core.extension.IExtension), as well as the default [CementExtensionHandler](https://docs.builtoncement.com/%7B%7B%20version%20%7D%7D/api/core/extension.html#cement.core.extension.CementExtensionHandler) that implements the interface. Its purpose is to manage loading framework extensions and making them usable by the application. Extensions are similar to [Application Plugins](https://docs.builtoncement.com/%7B%7B%20version%20%7D%7D/dev/plugins.html), but at the framework level \(application agnostic\).

{% hint style="warning" %}
Cement often includes multiple handler implementations of an interface that may or may not have additional features or functionality than the interface requires.  The documentation below only references usage based on the interface and default handler \(not the full capabilities of an implementation\).
{% endhint %}

**Extension Handlers Included with Cement:**

* ​[CementExtensionHandler](https://docs.builtoncement.com/%7B%7B%20version%20%7D%7D/api/core/extension.html#cement.core.extension.CementExtensionHandler)​

Please reference the [IExtension](https://docs.builtoncement.com/%7B%7B%20version%20%7D%7D/api/core/extension.html#cement.core.extension.IExtension) interface documentation for writing your own extension handler.

 As of Cement 2.1.3, optional extensions with external dependencies are now being shipped along with mainline sources. This means, that Cement Core continues to maintain a 100% zero dependency policy, however Framework Extensions \*can\* rely on external deps. It is the responsibility of the application developer to include these dependencies in their application \(as the Cement package does not include these dependencies\).

### Extension Configuration Settings {#extension-configuration-settings}

The following Meta settings are honored under the CementApp:

* **extension\_handler** - A handler class that implements the `IExtension` interface. This can be a string \(label of a registered handler\), an uninstantiated class, or an instantiated class object. Default: `CementExtensionHandler`.
* **core\_extensions** - List of Cement core extensions. These are generally required by Cement and should only be modified if you know what you're doing. Use `extensions` to add to this list, rather than overriding core extensions. That said if you want to prune down your application, you can remove core extensions if they are not necessary \(for example if using your own log handler extension you likely don't want/need `LoggingLogHandler` to be registered, but removing it really doesn't buy you much\).
* **extensions** - List of additional framework extensions to load.

The following example shows how to alter these settings for your application:

```text
from cement.core.foundation import CementAppfrom cement.core.ext import CementExtensionHandler​class MyExtensionHandler(CementExtensionHandler):    pass​class MyApp(CementApp):    class Meta:        label = 'myapp'        extension_handler = MyExtensionHandler        extensions = ['myapp.ext.ext_something_fancy']​with MyApp() as app:    app.run()
```

### Creating an Extension {#creating-an-extension}

The extension system is a mechanism for dynamically loading code to extend the functionality of the framework. In general, this includes the registration of interfaces, handlers, and/or hooks.

The following is an example extension that provides an Output Handler. We will assume this extension is part of our `myapp` application, and the extension module will be `myapp.ext.ext_myoutput` \(or whatever you want to call it\).

```text
from cement.core import handler, outputfrom cement.utils.misc import minimal_logger​LOG = minimal_logger(__name__)​class MyOutputHandler(output.CementOutputHandler):    class Meta:        label = 'myoutput'​    def render(self, data_dict, template=None):        LOG.debug("Rendering output via MyAppOutputHandler")        for key in data_dict.keys():            print "%s => %s" % (key, data_dict[key])​def load(app):    handler.register(MyOutputHandler)
```

Take note of two things. One is, the `LOG` we are using is from `cement.utils.misc.minimal_logger(__name__)`. Framework extensions do not use the application log handler, ever. Use the `minimal_logger()`, and only log to 'DEBUG' \(recommended\).

Secondly, in our extension file we need to define any interfaces, and register handlers and/or hooks if necessary. In this example we only needed to register our output handler \(which happens when the extension is loaded by the application\).

Last, notice that all `bootstrapping` code goes in a `load()` function. This is where registration of handlers/hooks should happen. For convenience, and certain edge cases, the `app` object is passed here in its current state at the time that `load()` is called.

You will notice that extensions are essentially the same as application plugins, however the difference is both when/how the code is loaded, as well as the purpose of that code. Framework extensions add functionality to the framework for the application to utilize, where application plugins extend the functionality of the application itself.

### Loading an Extension {#loading-an-extension}

Extensions are loaded when `setup()` is called on an application. Cement automatically loads all extensions listed under the applications `core_extensions` and `extensions` meta options.

To load the above example into our application, we just add it to the list of `extensions` \(not core extensions\). Lets assume the extension code lives in `myapp/ext/ext_something_fancy.py`:

```text
from cement.core.foundation import CementApp​class MyApp(CementApp):    class Meta:        label = 'myapp'        extensions = ['myapp.ext.ext_something_fancy']​with MyApp() as app:    app.run()
```

Note that Cement provides a shortcut for Cement extensions. For example, the following:

```text
CementApp('myapp', extensions=['json', 'daemon'])
```

Is equivalent to:

```text
CementApp('myapp',    extensions=[        'cement.ext.ext_json',        'cement.ext.ext_daemon',        ]    )
```

For non-cement extensions you need to use the full python 'dotted' module path.

### Loading Extensions Via a Configuration File {#loading-extensions-via-a-configuration-file}

Some use cases require that end-users are able to modify what framework extensions are loaded via a configuration file. The following gives an example of how an application can support an optional `extensions` configuration setting that will **append** extensions to `CementApp.Meta.extensions`.

Note that extensions loaded in this way will happen **after** the config handler is setup. Normally, extensions are loaded just before the configuration files are read. Therefore, some extensions may not be compatible with this method if they attempt to perform any actions before `app.setup()` completes \(such as in early framework hooks before configuration files are loaded\).

**myapp.py**

```text
from cement.core.foundation import CementApp​class MyApp(CementApp):    class Meta:        label = 'myapp'        config_files = [            './myapp.conf',            ]​def main():    with MyApp() as app:        app.run()​if __name__ == '__main__':    main()
```

**myapp.conf**

```text
[myapp]extensions = json, yaml
```

Which looks like:

```text
$ python myapp.py --helpusage: myapp.py (sub-commands ...) [options ...] {arguments ...}​MyApp Does Amazing Things​optional arguments:  -h, --help     show this help message and exit  --debug        toggle debug output  --quiet        suppress all output  -o {json,yaml} output format
```

Note the `-o` command line option that are provided by Cement allowing the end user to override the output handler with the available/loaded extensions \(that support this feature\).

