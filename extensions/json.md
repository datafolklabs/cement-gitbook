# Json



The JSON Extension adds the [`JsonOutputHandler`](http://cement.readthedocs.io/en/2.99/api/ext/ext_json/#cement.ext.ext_json.JsonOutputHandler) to render output in pure JSON, as well as the [`JsonConfigHandler`](http://cement.readthedocs.io/en/2.99/api/ext/ext_json/#cement.ext.ext_json.JsonConfigHandler) that allows applications to use JSON configuration files as a drop-in replacement of the default [`cement.ext.ext_configparser.ConfigParserConfigHandler`](http://cement.readthedocs.io/en/2.99/api/ext/ext_configparser/#cement.ext.ext_configparser.ConfigParserConfigHandler).

### Requirements

> * No external dependencies.

### Configuration

This extension does not support any configuration settings.

#### Usage

**myapp.conf**

```text
{
    "myapp": {
        "foo": "bar"
    }
}
```

**myapp.py**

```text
from cement import App

class MyApp(App):
    class Meta:
        label = 'myapp'
        extensions = ['json']
        config_handler = 'json'

        # you probably don't want this to be json by default.. but you can
        # output_handler = 'json'

with MyApp() as app:
    app.run()

    # create some data
    data = dict(foo=app.config.get('myapp', 'foo'))

    app.render(data)
```

In general, you likely would not set `output_handler` to `json`, but rather another type of output handler that display readable output to the end-user \(i.e. Mustache, or Tabulate\). By default Cement adds the `-o` command line option to allow the end user to override the output handler. For example: passing `-o json` will override the default output handler and set it to `JsonOutputHandler`.

See `App.Meta.handler_override_options`.

```text
$ python myapp.py -o json
{"foo": "bar"}
```

### What if I Want To Use UltraJson or Something Else?

It is possible to override the backend `json` library module to use, for example if you wanted to use UltraJson \(`ujson`\) or another **drop-in replacement** library. The recommended solution would be to override the `JsonOutputHandler` with you’re own sub-classed version, and modify the `json_module`meta-data option.

```text
from cement.ext.ext_json import JsonOutputHandler

class MyJsonHandler(JsonOutputHandler):
    class Meta:
        json_module = 'ujson'

# then, the class must be replaced via a 'post_setup' hook

def override_json(app):
    app.handler.register(MyJsonHandler, force=True)

app.hook.register('post_setup', override_json)
```

