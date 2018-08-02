---
description: Overview of notable features and major changes in the latest release.
---

# What's New!

## Notable Features and Changes

### Better Documentation

A lot of time and effort has gone into revamping the developer documentation, which includes:

* Separation of Developer Guide \(this\) and [API Reference](http://cement.readthedocs.io/en/2.99/)
* Complete [framework overview](../getting-started/framework-overview.md) and [beginner tutorial](../getting-started/beginner-tutorial/) to get started building your first application
* List of [terminology](../terminology.md) used throughout the framework and documentation
* Thorough guide of all [builtin extensions](../extensions/)

### Simplified API Naming Conventions and Usage

The naming conventions and library paths in Cement 2 were laborious to type and remember.  Now all modules/methods/etc are importable from `cement` namespace directly, and the naming is simplified \(ex: `CementApp` is now `App`, `CementHandler` is now `Handler`, etc\).

```python
from cement import App, Controller, ex

class Base(Controller):
    class Meta:
        label = 'base'

    @ex(help='this is a command')
    def cmd1(self):
        print('Inside Base.cmd1()')
    
class MyApp(App):
    class Meta:
        label = 'myapp'
        handlers = [Base]

```

### Developer Tools CLI

The [Cement Developer Tools](../getting-started/developer-tools.md) allow developers to quickly generate projects, plugins, extensions, and scripts:

```text
$ cement generate project ./myapp
INFO: Generating cement project in ./myapp/

$ cement generate plugin ./myapp/plugins/
INFO: Generating cement plugin in ./myapp/plugins/

$ cement generate extension ./myapp/ext/
INFO: Generating cement extension in ./myapp/ext/

$ cement generate script .
INFO: Generating cement script in .
```

### Clearer Interface Definition and Implementation

In Cement 2, the design of the interface and handler system was not easy to follow for new developers to the framework.  It was also loosely modeled after ZopeInterface that may have lead to some odd naming conventions \(IMeta, IMyInterface, etc\), and an implementation that just felt weird.

Interfaces are now defined using the standard library's [Abstract Base Class](https://docs.python.org/3/library/abc.html) module per the [request of the community](https://github.com/datafolklabs/cement/issues/192), moving the framework away from oddities and more toward common Python standards.  

### Docker / Docker Compose Support 

Cement now includes a fully functioning docker setup out-of-the-box for local development of the framework that includes all dependencies, and dependency services like Redis, Memcached, etc. 

Getting up and running is as simple as running the following:

```text
$ make dev

|> cement <| src #
```

This drops you into a shell within a docker container, and environment so that everything required to dev, and test is ready to roll:

```text
|> cement <| src # make test

|> cement <| src # make docs
```

_See the `Makefile` for more common development tasks \(for framework development\)._

### Environment Variable Overrides

Cement now supports the ability to override all config object settings via their associated environment variables.  For example:

{% tabs %}
{% tab title="myapp.py" %}
```python
from cement import App, init_defaults

defaults = init_defaults('myapp')
defaults['myapp']['foo'] = 'bar'

class MyApp(App):
    class Meta:
        label = 'myapp'
        config_defaults = defaults

with MyApp() as app:
    app.run()
    foo = app.config.get('myapp', 'foo')
    print('Foo => %s' % foo)
```
{% endtab %}

{% tab title="cli" %}
```text
$ python myapp.py
Foo => bar

$ export MYAPP_FOO='not-bar'

$ python myapp.py
Foo => not-bar
```
{% endtab %}
{% endtabs %}

Environment variables are logically mapped to configuration settings based on their config keys and are prefixed with `MYAPP_` \(based on the label of the app\).  So: 

* `config['myapp']['foo']` =&gt; `$MYAPP_FOO`
* `config['some_section']['foo']` =&gt; `$MYAPP_SOME_SECTION_FOO`

## New Interfaces

| \*\*\*\*[**Template**](../core-foundation/templating.md)\*\*\*\* | Rendering of template data \(content, files, etc\).  Existing output handler type plugins were also updated to include an associated template handler \(`MustacheTemplateHandler`, `Jinja2TemplateHandler`, etc\). |
| --- |


## New Extensions

| \*\*\*\*[**Print**](../extensions/print.md)\*\*\*\* | Used primarily in development as a replacement for standard `print()`, allowing the developer to honor framework features like `pre_render` and `post_render` hooks. |
| --- | --- | --- |
| \*\*\*\*[**Scrub**](../extensions/scrub.md)\*\*\*\* | Adds the ability to easily obfuscate sensitive data from rendered output \(think IP addresses, credit card numbers, etc\) |
| \*\*\*\*[**Generate**](../extensions/generate.md)\*\*\*\* | Adds the ability for application developers to add a `generate` controller to their application, and include any number of source templates to generate from.  Think `myapp generate plugin` for third party developers to create plugins for your application from a fully-functional working template. |

