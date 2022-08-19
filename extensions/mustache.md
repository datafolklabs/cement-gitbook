# Mustache

## Introduction

The Mustache Extension provides output and file templating based on the [Mustache Templating Language](http://mustache.github.com/).

**Documentation References:**

* [Output Rendering](../core-foundation/output-rendering.md)
* [Templating](../core-foundation/templating.md)

**API References**

* [Cement Mustache Extension](https://cement.readthedocs.io/en/3.0/api/ext/ext\_mustache/)
* [Mustache Library](http://mustache.github.io/)

## Requirements

* Pystache

{% hint style="info" %}
Cement 3.0.8+:

`pip install cement[mustache]`
{% endhint %}

{% hint style="warning" %}
Applications using Cement <3.0.8 should continue to include `pystache` in their dependencies.
{% endhint %}

## Configuration

### **Application Configuration Settings**

This extension honors the following settings under the primary namespace (ex: `[myapp]`) of the application configuration:

| **Setting**       | **Description**                               |
| ----------------- | --------------------------------------------- |
| **template\_dir** | Directory path of a local template directory. |

### **Application Meta Options**

This extension honors the following [`App.Meta`](http://cement.readthedocs.io/en/3.0/api/core/foundation/?highlight=app.meta#cement.core.foundation.App.Meta) options:

| **Option**            | **Description**                                         |
| --------------------- | ------------------------------------------------------- |
| **template\_handler** | A template handler to use as the backend for templating |
| **template\_dirs**    | A list of data directories to look for templates        |
| **template\_module**  | A python module to look for templates                   |

## Usage

### Output Handler

{% tabs %}
{% tab title="Example: Mustache Output Handler" %}
```python
from cement import App

class MyApp(App):
    class Meta:
        label = 'myapp'
        extensions = ['mustache']
        output_handler = 'mustache'

with MyApp() as app:
    app.run()

    # create some data
    data = {
        'foo': 'bar',
    }

    # render the data to STDOUT (default) via a template
    app.render(data, 'my_template.mustache')
```
{% endtab %}
{% endtabs %}

### **Template Handler**

{% tabs %}
{% tab title="Example: Using Mustache Template Handler" %}
```python
from cement import App

class MyApp(App):
    class Meta:
        label = 'myapp'
        extensions = ['mustache']
        template_handler = 'mustache'

with MyApp() as app:
    app.run()

    # create some data
    data = {
        'foo': 'bar'
    }

    # copy a source template directory to destination
    app.template.copy('/path/to/source/', 
                      '/path/to/destination/', 
                      data)

    # render any content as a template
    app.template.render('foo -> {{ foo }}', data)
```
{% endtab %}
{% endtabs %}

### Loading Partials

Mustache supports `partials`, or in other words template `includes`. These are also loaded by the output handler, but require a full file name. The partials will be loaded in the same way as the base templates.

{% tabs %}
{% tab title="Example: Using Mustache Partials" %}
{% code title="templates/base.mustache" %}
```python
Inside base.mustache
{{> partial.mustache}}
```
{% endcode %}

{% code title="templates/partial.mustache" %}
```
Inside partial.mustache
```
{% endcode %}
{% endtab %}
{% endtabs %}

The above would output:

```
Inside base.mustache
Inside partial.mustache
```
