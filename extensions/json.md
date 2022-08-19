# Json

## Introduction

The JSON Extension provides the [`JsonOutputHandler`](http://cement.readthedocs.io/en/3.0/api/ext/ext\_json/#cement.ext.ext\_json.JsonOutputHandler) for output rendering in pure JSON, as well as the [`JsonConfigHandler`](http://cement.readthedocs.io/en/3.0/api/ext/ext\_json/#cement.ext.ext\_json.JsonConfigHandler) that allows applications to use JSON configuration files as a drop-in replacement of the default [`ConfigParserConfigHandler`](http://cement.readthedocs.io/en/3.0/api/ext/ext\_configparser/#cement.ext.ext\_configparser.ConfigParserConfigHandler).

**Documentation References:**

* [Configuration Settings](../core-foundation/configuration-settings.md)
* [Output Rendering](../core-foundation/output-rendering.md)

**API References:**

* [Cement Json Extension](http://cement.readthedocs.io/en/3.0/api/ext/ext\_json/)
* [Python Json Library](https://docs.python.org/3/library/json.html)

## Requirements

* No external dependencies

## Configuration

This extension does not rely on any application level configuration settings or meta options.

### Using an Alternative Json Module

In some edge cases users have wanted to use an alternative json module for performance reasons, in particular [UltraJson](https://github.com/esnme/ultrajson). It is possible to override the backend `json` library module to use, such as `ujson` or another **drop-in replacement** library. The recommended solution would be to override the [`JsonOutputHandler.Meta.json_module`](http://cement.readthedocs.io/en/3.0/api/ext/ext\_json/#cement.ext.ext\_json.JsonConfigHandler.Meta.json\_module):

{% tabs %}
{% tab title="Example: Using an Alternative Json Module" %}
```python
from cement import App, init_defaults

META = init_defaults('output.json', 'config.json')
META['output.json']['json_module'] = 'ujson'
META['config.json']['json_module'] = 'ujson'

class MyApp(App):
    class Meta:
        label = 'myapp'
        extensions = ['json']
        meta_defaults = META
```
{% endtab %}
{% endtabs %}

## Usage

### Config Handler

{% tabs %}
{% tab title="Example: Using Json Config Handler" %}
{% code title="myapp.py" %}
```python
from cement import App

class MyApp(App):
    class Meta:
        label = 'myapp'
        extensions = ['json']
        config_handler = 'json'
        config_file_suffix = '.json'
```
{% endcode %}

{% code title="~/.myapp.json" %}
```
{
    "myapp": {
        "foo": "bar"
    }
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

### Output Handler

In general, you likely would not set `output_handler` to `json`, but rather another type of output handler that displays readable output to the end-user (ex: Mustache, Jinja2, or Tabulate). However, Cement supports overriding handlers via command line options if the [`Handler.Meta.overridable`](http://cement.readthedocs.io/en/3.0/api/core/handler/#cement.core.handler.Handler.Meta.overridable) option is set. For example, `-o json` will trigger the framework to use the `json` output handler, overriding the default set in [`App.Meta.output_handler`](http://cement.readthedocs.io/en/3.0/api/core/foundation/#cement.core.foundation.App.Meta.output\_handler).

See the documentation on [Overriding Handlers via Command Line](../core-foundation/interfaces-and-handlers.md#overriding-handlers-via-command-line).

{% tabs %}
{% tab title="Example: Using Json Output Handler" %}
{% code title="myapp.py" %}
```python
from cement import App, init_defaults

META = init_defaults('output.json')
META['output.json']['overridable'] = True

class MyApp(App):
    class Meta:
        label = 'myapp'
        extensions = ['json', 'mustache']
        meta_defaults = META
        output_handler = 'mustache'
        template_dir = './templates'

with MyApp() as app:
    app.run()

    data = {'foo': 'bar'}
    app.render(data, 'example.m')
```
{% endcode %}

{% code title="templates/example.m" %}
```
Foo: {{ foo }}
```
{% endcode %}
{% endtab %}

{% tab title="cli" %}
```
$ python myapp.py
Foo: bar

$ python myapp.py -o json
{"foo": "bar"}
```
{% endtab %}
{% endtabs %}
