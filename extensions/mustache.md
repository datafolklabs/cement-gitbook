# Mustache



The Mustache Extension provides output and file templating based on the [Mustache Templating Language](http://mustache.github.com/).

### Requirements

> * pystache \(`pip install pystache`\)

### Configuration

To **prepend** a directory to the `template_dirs` list defined by the application/developer, an end-user can add the configuration option `template_dir` to their application configuration file under the main config section:

```text
[myapp]
template_dir = /path/to/my/templates
```

Example

**Output Handler**

```text
from cement import App

class MyApp(App):
    class Meta:
        label = 'myapp'
        extensions = ['mustache']
        output_handler = 'mustache'
        template_module = 'myapp.templates'
        template_dirs = [
            '~/.myapp/templates',
            '/usr/lib/myapp/templates',
            ]
# ...
```

Note that the above `template_module` and `template_dirs` are the auto-defined defaults but are added here for clarity. From here, you would then put a Mustache template file in`myapp/templates/my_template.mustache` or `/usr/lib/myapp/templates/my_template.mustache` and then render a data dictionary with it:

```text
app.render(some_data_dict, 'my_template.mustache')
```

**Template Handler**

```text
from cement import App

class MyApp(App):
    class Meta:
        label = 'myapp'
        extensions = ['mustache']
        template_handler = 'mustache'

with MyApp() as app:
    app.run()

    # create some data
    data = dict(foo='bar')

    # copy a source template directory to destination
    app.template.copy('/path/to/source/', '/path/to/destination/', data)

    # render any content as a template
    app.template.render('foo -> {{ foo }}', data)
```

### Loading Partials

Mustache supports `partials`, or in other words template `includes`. These are also loaded by the output handler, but require a full file name. The partials will be loaded in the same way as the base templates

For example:

**templates/base.mustache**

```text
Inside base.mustache
{{> partial.mustache}}
```

**template/partial.mustache**

```text
Inside partial.mustache
```

Would output:

```text
Inside base.mustache
Inside partial.mustache
```

