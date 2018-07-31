# Yaml



The Yaml Extension adds the [`YamlOutputHandler`](http://cement.readthedocs.io/en/2.99/api/ext/ext_yaml/#cement.ext.ext_yaml.YamlOutputHandler) to render output in pure Yaml, as well as the [`YamlConfigHandler`](http://cement.readthedocs.io/en/2.99/api/ext/ext_yaml/#cement.ext.ext_yaml.YamlConfigHandler) that allows applications to use Yaml configuration files as a drop-in replacement of the default [`cement.ext.ext_configparser.ConfigParserConfigHandler`](http://cement.readthedocs.io/en/2.99/api/ext/ext_configparser/#cement.ext.ext_configparser.ConfigParserConfigHandler).

### Requirements

> * pyYaml \(`pip install pyYaml`\)

### Configuration

This extension does not honor any application configuration settings.

#### Usage

**myapp.conf**

```text
---
myapp:
    foo: bar
```

**myapp.py**

```text
from cement import App

class MyApp(App):
    class Meta:
        label = 'myapp'
        extensions = ['yaml']
        config_handler = 'yaml'

        # you probably don't want to do this.. but you can
        # output_handler = 'yaml'

with MyApp() as app:
    app.run()

    # create some data
    data = dict(foo=app.config.get('myapp', 'foo'))

    app.render(data)
```

In general, you likely would not set `output_handler` to `yaml`, but rather another type of output handler that displays readable output to the end-user \(i.e. Mustache, or Tabulate\). By default Cement adds the `-o` command line option to allow the end user to override the output handler. For example: passing `-o yaml` will override the default output handler and set it to `YamlOutputHandler`.

See `App.Meta.handler_override_options`.

```text
$ python myapp.py -o yaml
{foo: bar}
```

