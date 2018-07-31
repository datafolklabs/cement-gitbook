# Handlebars

The Handlebars Extension provides output and file templating using the [pybars3](https://github.com/wbond/pybars3) package.

### Requirements

> * pybars3 \(`pip install pybars3`\)

### Configuration

#### Application Meta-data

This extension supports the following application meta-data via `App.Meta`:

> * **handlebars\_helpers** - A dictionary of helper functions to register with the compiler. Will **override** `HandlebarsOutputHandler.Meta.helpers`.
> * **handlebars\_partials** - A list of partials \(template file names\) to search for, and pre-load before rendering templates. Will **override** `HandlebarsOutputHandler.Meta.partials`.

#### Template Directories

To **prepend** a directory to the `template_dirs` list defined by the application/developer, an end-user can add the configuration option `template_dir` to their application configuration file under the main config section:

```text
[myapp]
template_dir = /path/to/my/templates
```

Example

**Output Handler**

```text
class MyApp(App):
    class Meta:
        label = 'myapp'
        extensions = ['handlebars']
        output_handler = 'handlebars'
        template_module = 'myapp.templates'
        template_dirs = [
            '~/.myapp/templates',
            '/usr/lib/myapp/templates',
            ]
# ...
```

Note that the above `template_module` and `template_dirs` are the auto-defined defaults but are added here for clarity. From here, you would then put a Handlebars template file in`myapp/templates/my_template.handlebars` or `/usr/lib/myapp/templates/my_template.handlebars` and then render a data dictionary with it:

```text
app.render(some_data, 'my_template.handlebars')
```

**Template Handler**

```text
from cement import App

class MyApp(App):
    class Meta:
        label = 'myapp'
        extensions = ['handlebars']
        template_handler = 'handlebars'

with MyApp() as app:
    app.run()

    # create some data
    data = dict(foo='bar')

    # copy a source template directory to destination
    app.template.copy('/path/to/source/', '/path/to/destination/', data)

    # render any content as a template
    app.template.render('foo -> {{ foo }}', data)
```

#### Helpers

Custom helper functions can easily be registered with the compiler via`App.Meta.handlebars_helpers` and/or `HandlebarsOutputHandler.Meta.helpers`.

```text
def my_custom_helper(this, arg1, arg2):
    # do something with arg1 and arg2
    if arg1 == arg2:
        return True
    else:
        return False


class MyApp(App):
    class Meta:
        label = 'myapp'
        extensions = ['handlebars']
        output_handler = 'handlebars'
        handlebars_helpers = {
            'myhelper' : my_custom_helper
        }
# ...
```

You would then access this in your template as:

```text
This is my template

{{#if (myhelper this that)}}
This will only appear if myhelper returns True
{{/if}}
```

See the [Handlebars Documentation](https://github.com/wbond/pybars3) for more information on helpers.

#### Partials

Though partials are supported by the library, there is no good way of automatically loading them in the context and workflow of a typical Cement application. Therefore, the extension needs a list of partial template names to know what to preload, in order to make partials work. Future versions will hopefully automate this.

Example:

```text
class MyApp(App):
    class Meta:
        label = 'myapp'
        extensions = ['handlebars']
        output_handler = 'handlebars'
        handlebars_partials = [
            'header.bars',
            'footer.bars',
        ]
```

Where `header.bars` and `footer.bars` are template names that will be searched for, and loaded just like other templates loaded from template dirs. These are then referenced in templates as:

```text
{{> "header.bars"}}
This is my template
{{> "footer.bars}}
```

See the [Handlebars Documentation](https://github.com/wbond/pybars3) for more information on partials.

