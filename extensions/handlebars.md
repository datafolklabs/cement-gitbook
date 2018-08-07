# Handlebars

## Introduction

The Handlebars Extension provides output and file templating using the [pybars3](https://github.com/wbond/pybars3) package.

**Documentation References:**

* [Output Rendering](../core-foundation/output-rendering.md)
* [Templating](../core-foundation/templating.md)

**API References:**

* [Cement Handlebars Extension](http://cement.readthedocs.io/en/2.99/api/ext/ext_handlebars/)

## Requirements

* pybars3 \(`pip install pybars3`\)

## Configuration

### **Application Configuration Settings**

This extension honors the following settings under the primary namespace \(ex: `[myapp]`\) of the application configuration:

| **Setting** | **Description** |
| :--- | :--- |
| **template\_dir** | Directory path of a local template directory. |

### **Application Meta Options**

This extension honors the following [`App.Meta`](http://cement.readthedocs.io/en/2.99/api/core/foundation/?highlight=app.meta#cement.core.foundation.App.Meta) options:

| **Option** | **Description** |
| :--- | :--- |
| **template\_handler** | A template handler to use as the backend for templating |
| **template\_dirs** | A list of data directories to look for templates |
| **template\_module** | A python module to look for templates |

### Template Directories

To **prepend** a directory to the [`App.Meta.emplate_dirs`](http://cement.readthedocs.io/en/2.99/api/core/foundation/#cement.core.foundation.App.Meta.template_dirs) list defined by the application/developer, an end-user can add the configuration option `template_dir` to their application configuration file under the main config section:

```text
[myapp]
template_dir = /path/to/my/templates
```

## Usage

### **Output Handler**

{% tabs %}
{% tab title="Example: Using HandlebarsOutputHandler" %}
{% code-tabs %}
{% code-tabs-item title="myapp.py" %}
```python
from cement import App

class MyApp(App):
    class Meta:
        label = 'myapp'
        extensions = ['handlebars']
        output_handler = 'handlebars'

with MyApp() as app:
    app.run()
    
    data = {
        'foo': 'bar'
    }
    
    app.render(data, 'my_template.handlebars')
```
{% endcode-tabs-item %}

{% code-tabs-item title="templates/example.handlebars" %}
```
Example Handlebars Template
Foo => {{ foo }}
```
{% endcode-tabs-item %}
{% endcode-tabs %}
{% endtab %}

{% tab title="cli" %}
```text
$ python myapp.py
Example Handlebars Template
Foo => bar
```
{% endtab %}
{% endtabs %}

### **Template Handler**

{% tabs %}
{% tab title="Example: Using Handlebars Template Handler" %}
```python
from cement import App

class MyApp(App):
    class Meta:
        label = 'myapp'
        extensions = ['handlebars']
        template_handler = 'handlebars'

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
    app.template.render('Foo => {{ foo }}', data)
```
{% endtab %}
{% endtabs %}

### Helpers

{% tabs %}
{% tab title="Example: Using Handlebars Helpers" %}
```python
from cement import App, init_defaults

def my_custom_helper(this, arg1, arg2):
    # do something with arg1 and arg2
    if arg1 == arg2:
        return True
    else:
        return False

META = init_defaults('output.handlebars')
META['output.handlebars']['helpers'] = {
    'myhelper': my_custom_helper,
}

class MyApp(App):
    class Meta:
        label = 'myapp'
        extensions = ['handlebars']
        output_handler = 'handlebars'
        meta_defaults = META
```
{% endtab %}

{% tab title="Second Tab" %}

{% endtab %}
{% endtabs %}

You would then access this in your template as:

```text
This is my template

{{#if (myhelper this that)}}
This will only appear if myhelper returns True
{{/if}}
```

See the [Handlebars Documentation](https://github.com/wbond/pybars3) for more information on helpers.

### Partials

Though partials are supported by the library, there is no good way of automatically loading them in the context and workflow of a typical Cement application. Therefore, the extension needs a list of partial template names to know what to preload, in order to make partials work. Future versions will hopefully automate this.

{% tabs %}
{% tab title="Example: Using Handlebars Partials" %}
{% code-tabs %}
{% code-tabs-item title="myapp.py" %}
```python
from cement import App, init_defaults

META = init_defaults('output.handlebars')
META['output.handlebars']['partials'] = [
    'header.bars',
    'footer.bars',
]

class MyApp(App):
    class Meta:
        label = 'myapp'
        extensions = ['handlebars']
        output_handler = 'handlebars'
        meta_defaults = META
```
{% endcode-tabs-item %}
{% endcode-tabs %}
{% endtab %}

{% tab title="Second Tab" %}

{% endtab %}
{% endtabs %}

Where `header.bars` and `footer.bars` are template names that will be searched for, and loaded just like other templates loaded from template dirs. These are then referenced in templates as:

```text
{{> "header.bars"}}
This is my template
{{> "footer.bars}}
```

See the [Handlebars Documentation](https://github.com/wbond/pybars3) for more information on partials.

