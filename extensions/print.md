# Print



The Print Extension adds the [`PrintOutputHandler`](http://cement.readthedocs.io/en/2.99/api/ext/ext_print/#cement.ext.ext_print.PrintOutputHandler) and [`PrintDictOutputHandler`](http://cement.readthedocs.io/en/2.99/api/ext/ext_print/#cement.ext.ext_print.PrintDictOutputHandler) to render output in pure text. It is mostly intended for development, but also supports the additional `app.print()`extended function which can be used in place of the standard `print()` so that apps can continue to utilitize features of the framework consistently \(such as honoring `pre_render` and `post_render`hooks, etc\).

### Requirements

> * No external dependencies.

### Configuration

This extension does not support any configuration settings.

#### Usage

**myapp.py**

```text
from cement import App


class MyApp(App):
    class Meta:
        label = 'myapp'
        extensions = ['print']


with MyApp() as app:
    app.run()

    ### will print the text using PrintOutputHandler (honors render hooks)

    app.print('This is an output message')


    ### using PrintOutputHandler directly (not likely, but for reference)

    data = {}
    data['out'] = 'This is an output message'
    app.render(data)
```

```text
$ python myapp.py
This is an output message
```

Alternatively, you can use the `print_dict` output handler that can be useful in development as it simply just prints out a string representation of the data dict.

**myapp.py**

```text
from cement import App


class MyApp(App):
    class Meta:
        label = 'myapp'
        extensions = ['print_dict']


with MyApp() as app:
    app.run()

    data = {'foo' : 'bar'}
    app.render(data)
```

```text
$ python myapp.py
foo: bar
```

