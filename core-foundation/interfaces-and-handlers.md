# Interfaces and Handlers

## Introduction to Interfaces

Cement builds upon a standard interface and handler system that is used extensively to break up pieces of the framework into relatable chucks, and allow customization of everything from logging to config file parsing, and almost every operation in between.

{% hint style="info" %}
In Cement, an interface is what **defines** some functionality, and a handler is what **implements** that functionality.
{% endhint %}

We call the implementation of an interface a **handler**, and provide the ability to easily register and retrieve them via the `app.handler` object.  Cement interfaces are defined as [Python Abstract Base Classes](https://docs.python.org/3/library/abc.html), and handlers implement them by sub-classing and overriding the defined abstract methods required to make the implementation legit.

### Builtin Interfaces

The following interfaces are builtin to Cement's core foundation:

| \*\*\*\*[**Extension**](https://cement.readthedocs.io/en/portland/api/core/extension/#cement.core.extension.ExtensionHandler)  **** | Framework extension loading. |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| \*\*\*\*[**Log**](https://cement.readthedocs.io/en/portland/api/core/log/#cement.core.log.LogHandler)\*\*\*\* | Messaging to console, and/or file via common log facilities \(INFO, WARNING, ERROR, FATAL, DEBUG\). |
| \*\*\*\*[**Config**](https://cement.readthedocs.io/en/portland/api/core/config/#cement.core.config.ConfigHandler)\*\*\*\* | Merging of application configuration defaults, configuration files, and environment settings into a single config object. |
| \*\*\*\*[**Mail**](https://cement.readthedocs.io/en/portland/api/core/mail/#cement.core.mail.MailHandler)\*\*\*\* | Remote message sending \(email, smtp, etc\). |
| \*\*\*\*[**Plugin**](https://cement.readthedocs.io/en/portland/api/core/plugin/#cement.core.plugin.PluginHandler)\*\*\*\* | Application plugin loading. |
| \*\*\*\*[**Template**](https://cement.readthedocs.io/en/portland/api/core/template/#cement.core.template.TemplateHandler)\*\*\*\* | Rendering of template data \(content, files, etc\). |
| \*\*\*\*[**Output**](https://cement.readthedocs.io/en/portland/api/core/output/#cement.core.output.OutputHandler)\*\*\*\* | Rendering of data/content to end-user output \(console text from template, JSON, Yaml, etc\).  Often uses an associated template handler backend. |
| \*\*\*\*[**Argument**](https://cement.readthedocs.io/en/portland/api/core/arg/#cement.core.arg.ArgumentHandler)\*\*\*\* | Command line argument/option parsing. |
| \*\*\*\*[**Controller**](https://cement.readthedocs.io/en/portland/api/core/controller/#cement.core.controller.ControllerHandler)\*\*\*\* | Command dispatch \(sub-commands, arguments, etc\) |
| \*\*\*\*[**Cache**](https://cement.readthedocs.io/en/portland/api/core/cache/#cement.core.cache.CacheHandler)\*\*\*\* | Key/Value data store \(memcached, redis, etc\) |

### Working With Interfaces

The `InterfaceManager` \(`app.interface`\) provides quick mechanisms to list, get, and verify interfaces:

```python
from cement import App

with App('myapp') as app:
    # list defined interfaces
    app.interface.list()

    # get an interface class
    i = app.interface.get('output')

    # validate if an interface is defined
    app.interface.defined('mail')
```

### Defining an Interface

{% hint style="warning" %}
Defining interfaces is more of an advanced topic, and is not required to fully grasp for new developers or those new to the framework. 
{% endhint %}

Cement uses interfaces and handlers extensively to manage the framework, however developers can also make use of this system to provide a clean, and standardized way of allowing other developers to customize their application \(generally via application plugins\).

The following defines a basic interface we'll call `greeting`:

```python
from abc import abstractmethod
from cement import App, Interface

​class GreetingInterface(Interface):    
    class Meta:     
        interface = 'greeting'   
        
    @abstractmethod
    def _get_greeting(self):
        """
        Get a greeting message for the end-user.
        
        Returns:
            greeting (str): The greeting string to present to
                the end-user.
        """
        pass
        
    
    @abstractmethod
    def greet(self):
        """
        Display a greeting message for the end-user.
        
        Returns: None
        """
        pass

class MyApp(App):
    label = 'myapp'
    interfaces = [
        GreetingInterface,
    ]
```

The above example defines the `greeting` interface, by providing abstract methods that any handlers implementing this interface must provide. It does not implement any functionality on it's own \(though it could\), but rather defines and documents it's purpose and it's expected implementation.  The interface is easily defined with the framework by listing it in `App.Meta.interfaces`, but you can also define interfaces directly with `app.interface.define()`.

### Implementing an Interface

In order to implement the above `greeting` interface, we first want to provide a handler base class that will be the starting point for all implementations that sub-class from it:

```python
from cement import Handler

class GreetingHandler(GreetingInterface, Handler):

    def greet(self):
        self.app.log.debug('about to greet end-user')
        msg = self._get_greeting()
        assert isinstance(str, msg), "The msg is not a string!"
        print(msg)
```

{% hint style="info" %}
Handler base classes sub-class from both the interface they are implementing \(`GreetingInterface`\), and also the Cement Handler base class \(`Handler`\).  The application developer defining the interface should always provide a handler base class for the implementation, even if the base class does not fully satisfy the interface.
{% endhint %}

In the above example, the developer would call the `greet()` method that will do all of the common operations like logging, exception handling, etc for all implementations, leaving only the minimal `_get_greeting()` method to be provided by the final implementation sub-classes.  This follows the common [Don't Repeat Yourself \(DRY\)](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) principle's best practice \(all-be-it a tediously simple example\), where all of the reusable logic can live in one place, and sub-classes only focus on their unique means of implementing an interface.

In order to complete our implementation of the above `greeting` interface, we can now sub-class from the provided `GreetingHandler` base class, and fill in the missing pieces:

```python
class Hello(GreetingHandler):
    class Meta:
        label = 'hello'
    
    def _get_greeting(self):
        return 'Hello!'


class Goodbye(GreetingHandler):
    class Meta:
        label = 'goodbye'
    
    def _get_greeting(self):
        return 'Goodbye!'

```

{% hint style="info" %}
An interface defines itself to the framework via the `Interface.Meta.interface` string, and a handler defines itself via the `Handler.Meta.label` string.  Collectively, all implementation handlers are referred to by both as `<interface>.<label>` or in the above examples `greeting.hello` and `greeting.goodbye`.
{% endhint %}

### Putting It All Together

The following is an MVCE of defining an interface, providing an implementation handler base class, and registering multiple handlers that implement the interface differently:

{% tabs %}
{% tab title="myapp.py" %}
```python
from abc import abstractmethod
from cement import App, Interface, Handler

class GreetingInterface(Interface):
    class Meta:
        interface = 'greeting'

    @abstractmethod
    def _get_greeting(self):
        """
        Get a greeting message for the end-user.

        Returns:
            greeting (str): The greeting string to present to
                the end-user.
        """
        pass

    @abstractmethod
    def greet(self):
        """
        Display a greeting message for the end-user.
        
        Returns: None
        """
        pass


class GreetingHandler(GreetingInterface, Handler):

    def greet(self):
        self.app.log.debug('about to greet end-user')
        msg = self._get_greeting()
        assert isinstance(msg, str), "The msg is not a string!"
        print(msg)


class Hello(GreetingHandler):
    class Meta:
        label = 'hello'

    def _get_greeting(self):
        return 'Hello!'


class Goodbye(GreetingHandler):
    class Meta:
        label = 'goodbye'

    def _get_greeting(self):
        return 'Goodbye!'


class MyApp(App):
    class Meta:
    	label = 'myapp'

    	interfaces = [
        	GreetingInterface,
    	]

    	handlers = [
        	Hello,
        	Goodbye,
    	]

with MyApp() as app:
    app.run()
    g = app.handler.get('greeting', 'hello', setup=True)
    g.greet()
```
{% endtab %}

{% tab title="cli" %}
```text
$ python myapp.py
Hello!

$ python myapp.py -l debug
2018-07-17 20:28:21,449 (DEBUG) myapp : about to greet end-user
Hello!
```
{% endtab %}
{% endtabs %}

## Introduction to Handlers

Where an interface defines what an implementation is expected to provide, a handler is what actually implements the interface.

### Working with Handlers

The `HandlerManager` \(`app.handler`\) provides quick mechanisms to list, get, resolve and verify handlers. The following are a few examples of working with handlers:

```python
from cement import App

​with App('myapp') as app:    
    # get a log handler called logging
    lh = app.handler.get('log', 'logging', setup=True)​    
    
    # List all handlers registered to the config interface    
    app.handler.list('config')​    
    
    # check if the handler argparse is registered 
    # to the argument interface    
    app.handler.registered('argument', 'argparse')
    
    # resolve a handler by string, class, or object
    app.handler.resolve('log', 'logging')
    app.handler.resolve('log', LoggingLogHandler)
    app.handler.resolve('log', LoggingLogHandler())
```

{% hint style="info" %}
It is important to note that handlers are stored within the application as uninstantiated objects. Meaning you must instantiate them after retrieval \(call `_setup(app)`\) or simply pass `setup=True` to the `app.handler.get()` method.
{% endhint %}

### Registering Handlers to an Interface

An interface simply defines what an implementation is expected to provide, where a handler actually implements the interface. The following example is a handler that implements the MyInterface above:

```text
from cement.core.foundation import CementAppfrom cement.core.handler import CementBaseHandlerfrom myapp.interfaces import MyInterface​class MyHandler(CementBaseHandler):    class Meta:        interface = MyInterface        label = 'my_handler'        description = 'This handler implements MyInterface'        config_defaults = dict(            foo='bar'            )​    my_var = 'This is my var'​    def __init__(self):        self.app = None​    def _setup(self, app_obj):        self.app = app_obj​    def do_something(self):        print "Doing work!"​class MyApp(CementApp):    class Meta:        label = 'myapp'        handlers = [MyHandler]
```

Alternatively, if you need more control you might use this approach:

```text
from cement.core.foundation import CementApp​with CementApp('myapp') as app:    # register handler after the app is created    app.handler.register(MyHandler)​    app.run()
```

The above is a simple class that meets all the expectations of the interface. When calling `CementApp.handler.register()`, `MyHandler` is passed to the validator \(if defined in the interface\) and if it passes validation will be registered into `HandlerManager.__handlers__`.

### Overriding Default Handlers {#overriding-default-handlers}

Cement sets up a number of default handlers for logging, config parsing, etc. These can be overridden in a number of ways. The first way is by passing them as keyword arguments to `CementApp`:

```text
from cement.core.foundation import CementAppfrom myapp.log import MyLogHandler​# Create the applicationapp = CementApp('myapp', log_handler=MyLogHandler)app.setup()app.run()app.close()
```

The second way to override a handler is by setting it directly in the `CementApp` meta data:

```text
from cement.core.foundation import CementAppfrom myapp.log import MyLogHandler​class MyApp(CementApp):    class Meta:        label = 'myapp'        log_handler = MyLogHandler​with MyApp() as app:    app.run()
```

There are times that you may want to pre-instantiate handlers before passing them to CementApp\(\). The following works just the same:

```text
from cement.core.foundation import CementAppfrom myapp.log import MyLogHandler​my_log = MyLogHandler(some_param='some_value')​class MyApp(CementApp):    class Meta:        label = 'myapp'        log_handler = my_log​with MyApp() as app:    app.run()
```

To see what default handlers can be overridden, see the [cement.core.foundation](https://docs.builtoncement.com/2.10/api/cement.core.foundation) documentation.

### Multiple Registered Handlers {#multiple-registered-handlers}

All handlers and interfaces are unique. In most cases, where the framework is concerned, only one handler is used. For example, whatever is configured for the `log_handler` will be used and setup as `app.log`. However, take for example an Output Handler. You might have a default `output_handler` of `mustache`' \(a text templating language\) but may also want to override that handler with the `json` output handler when `-o json` is passed at command line. In order to allow this functionality, both the `mustache` and `json` output handlers must be registered.

Any number of handlers can be registered to an interface. You might have a use case for an Interface/Handler that may provide different compatibility base on the operating system, or perhaps based on simply how the application is called. A good example would be an application that automates building packages for Linux distributions. An interface would define what a build handler needs to provide, but the build handler would be different based on the OS. The application might have an `rpm` build handler, or a `dpkg` build handler to perform the build process differently.

### Customizing Handlers {#customizing-handlers}

The most common way to customize a handler is to subclass it, and then pass it to `CementApp`:

```text
from cement.core.foundation import CementAppfrom cement.lib.ext_logging import LoggingLogHandler​class MyLogHandler(LoggingLogHandler):    class Meta:        label = 'mylog'​    def info(self, msg):        # do something to customize this function, here...        super(MyLogHandler, self).info(msg)​app = CementApp('myapp', log_handler=MyLogHandler)
```

### Handler Default Configuration Settings {#handler-default-configuration-settings}

All handlers can define default config file settings via their `config_defaults` meta option. These will be merged into the `app.config` under the `[handler_interface].[handler_label]` section. These settings are overridden in the following order.

* The config\_defaults dictionary passed to `CementApp`
* Via any application config files with a `[handler_interface].[handler_type]` block \(i.e. `cache.memcached`\)

The following shows how to override defaults by passing them with the defaults dictionary to `CementApp`:

```text
from cement.core import foundationfrom cement.utils.misc import init_defaults​defaults = init_defaults('myinterface.myhandler')defaults['myinterface.myhandler'] = dict(foo='bar')app = foundation.CementApp('myapp', config_defaults=defaults)
```

Cement will use all defaults set via `MyHandler.Meta.config_defaults` \(for this example\), and then override just what is passed via `config_defaults['myinterface.myhandler']`. You should use this approach only to modify the global defaults for your application. The second way is to then set configuration file defaults under the `[myinterface.myhandler]` section. For example:

**my.config**

```text
[myinterface.myhandler]foo = bar
```

In the real world this may look like `[cache.memcached]`, or `[database.mysql]` depending on what the interface label, and handler label's are. Additionally, individual handlers can override their config section by setting `Meta.config_section`.

### Overriding Handlers Via Command Line {#overriding-handlers-via-command-line}

In some use cases, you will want the end user to have access to override the default handler of a particular interface. For example, Cement ships with multiple Output Handlers including `json`, `yaml`, and `mustache`. A typical application might default to using `mustache` to render console output from text templates. That said, without changing any code in the application, the end user can simply pass the `-o json` command line option and output the same data that is rendered to template, out in pure JSON.

The only built-in handler override that Cement includes is for the above mentioned example, but you can add any that your application requires.

The following example shows this in action... note that the following is already setup by Cement, but we're putting it here for clarity:

```text
from cement.core.foundation import CementApp​class MyApp(CementApp):    class Meta:        label = 'myapp'​        # define what extensions we want to load        extensions = ['mustache', 'json', 'yaml']​        # define our default output handler        output_handler = 'mustache'​        # define our handler override options        handler_override_options = dict(            output = (['-o'], dict(help='output format')),            )​​with MyApp() as app:    # run the application    app.run()​    # define some data for the output handler    data = dict(foo='bar')​    # render something using out output handlers, using mustache by    # default which will use the default.m templae    app.render(data, 'default.m')
```

Note what we see at command line:

```text
$ python myapp.py --helpusage: myapp.py [-h] [--debug] [--quiet] [-o {yaml,json}]​optional arguments:  -h, --help      show this help message and exit  --debug         toggle debug output  --quiet         suppress all output  -o {yaml,json}  output format
```

Notice the `-o` command line option, that includes the choices: `yaml` and `json`. This feature will include all Output Handlers that have the `overridable` meta-data option set to `True`. The MustacheOutputHandler does not set this option, therefore it does not show up as a valid choice.

Now what happens when we run it?

```text
$ python myapp.pyThis text is being rendered via Mustache.The value of the 'foo' variable is => 'bar'
```

The above is the default output, using `mustache` as our `output_handler`, and rendering the output text from a template called `default.m`. We can now override the output handler using the `-o` option and modify the output format:

```text
$ python myapp.py -o json{"foo": "bar"}
```

Again, any handler can be overridden in this fashion.

## API References

{{book}}

* ​[Cement Interface Module](https://docs.builtoncement.com/2.10/api/cement.core.interface)​
* ​[Cement Handler Module](https://docs.builtoncement.com/2.10/api/cement.core.handler)​



