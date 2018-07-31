# Jinja2



The Jinja2 Extension module provides output and file templating based on the [Jinja2 Templating Language](http://jinja.pocoo.org/).

### Requirements

> * Jinja2 \(`pip install Jinja2`\)

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
        extensions = ['jinja2']
        output_handler = 'jinja2'
        template_module = 'myapp.templates'
        template_dirs = [
            '~/.myapp/templates',
            '/usr/lib/myapp/templates',
            ]

with MyApp() as app:
    app.run()

    # create some data
    data = dict(foo='bar')

    # render the data to STDOUT (default) via a template
    app.render(data, 'my_template.jinja2')
```

Note that the above `template_module` and `template_dirs` are the auto-defined defaults but are added here for clarity. From here, you would then put a Jinja2 template file in`myapp/templates/my_template.jinja2` or `/usr/lib/myapp/templates/my_template.jinja2`.

**Template Handler**

```text
from cement import App

class MyApp(App):
    class Meta:
        label = 'myapp'
        extensions = ['jinja2']
        template_handler = 'jinja2'

with MyApp() as app:
    app.run()

    # create some data
    data = dict(foo='bar')

    # copy a source template directory to destination
    app.template.copy('/path/to/source/', '/path/to/destination/', data)

    # render any content as a template
    app.template.render('foo -> {{ foo }}', data)
```

