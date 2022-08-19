# Unit Testing

## Introduction

Unit testing Cement apps is as straight forward as any other Python library. Cement uses, and recommends the following utilities, but there are no limitations to what tools you choose to use:

* [Pytest](https://docs.pytest.org/en/7.1.x/): Unit testing.
* [Coverage](https://coverage.readthedocs.io/en/6.4.1/): Monitors code while unit tests run to ensure all code is tested.
* [Flake8](https://flake8.pycqa.org/en/latest/): Style-guide enforcement

Cement has a strict policy that all framework code has 100% test coverage, and 100% style-guide clearance before any releases are published. This is not required for applications built on Cement, but it is highly recommended for quality.

For examples, feel free to browse the [framework testing library](https://github.com/datafolklabs/cement/tree/main/tests) on Github.



## Special Considerations

### Sub-class TestApp

Cement apps sub-class from [`cement.core.foundation.App`](https://cement.readthedocs.io/en/3.0/api/core/foundation/#cement.core.foundation.App), however for testing there are some things we want to override to make things more portable/reliable. For example, in testing we do not want to parse `~/.myapp.yml` (example local user configuration file), as this may skew tests from one developer machine to the next.

Cement includes an additional [`cement.core.foundation.TestApp`](https://cement.readthedocs.io/en/3.0/api/core/foundation/#cement.core.foundation.TestApp) that can also be sub-classed to easily override App.Meta for testing.

This looks something like:

```python
class TestApp(App):
    class Meta:
        label = "app-%s" % misc.rando()[:12]
        argv = []
        core_system_config_files = []
        core_user_config_files = []
        config_files = []
        core_system_config_dirs = []
        core_user_config_dirs = []
        config_dirs = []
        core_system_template_dirs = []
        core_user_template_dirs = []
        core_system_plugin_dirs = []
        core_user_plugin_dirs = []
        plugin_dirs = []
        exit_on_close = False
```

To use this class, it is best to create a separate `MyAppTest` class that sub-classes both from `TestApp` and your `MyApp`:

```python
class MyAppTest(TestApp,MyApp):
    """A sub-class of MyApp that is better suited for testing."""

    class Meta:
        label = 'myapp'
```

{% hint style="info" %}
Note the order of sub-classing is important so that `Meta` is overridden properly.
{% endhint %}

### CLI Arguments and Options

As most apps built on Cement are command-line based, passing arguments and options to `MyApp` is important. This is easily handled by passing the [`argv`](https://cement.readthedocs.io/en/3.0/api/core/foundation/#cement.core.foundation.App.Meta.argv) list:

```python
myargs = ['some-command', '--some-option', '--etc']
with MyAppTest(argv=myargs) as app:
    app.run()
```

The above is equivalent to running the following at the command line:

```
myapp some-command --some-option --etc
```



### Temp Files/Dirs

Working with files and directories is common for apps built on Cement, which can be easily done using the [`fs.Tmp()`](https://cement.readthedocs.io/en/3.0/api/utils/fs/#cement.utils.fs.Tmp) utility in combination with [Pytest fixtures](https://docs.pytest.org/en/6.2.x/fixture.html). These fixtures can be automatically loaded from `tests/conftest.py`:

_myapp/tests/conftest.py:_

```python
import pytest
from cement import fs

@pytest.fixture(scope="function")
def tmp(request):
    """
    Create a `tmp` object that generates a unique temporary directory, and file
    for each test function that requires it.
    """
    t = fs.Tmp()
    yield t
    t.remove()
```

This creates a `tmp` fixture that we can use anywhere in our tests, and most importantly when the test is complete the temp object (file and directory) are automatically removed.

myapp/tests/test\_myapp.py:

```python
def test_myapp(tmp):
    with MyAppTest() as app:
        # do something with tmp.file/tmp.dir
        app.run()
```

{% hint style="info" %}
Note the `tmp` fixture is passed to each test function, creating a unique instance of it for each test.
{% endhint %}

## Writing Tests

In Pytest, tests are loaded from files matching `test_`, and functions matching `test_`. Some developers prefer to keep tests alongside the file it is testing (for example: `main.py` and `test_main.py`). Cement developers prefer putting all tests in a separate `tests/` directory:

```
├── CHANGELOG.md
├── README.md
├── myapp
│   ├── __init__.py
│   ├── controllers
│   │   ├── __init__.py
│   │   └── base.py
│   ├── main.py
├── requirements.txt
├── setup.py
└── tests
    ├── conftest.py
    ├── controllers
    │   └── test_base.py
    └── test_main.py
```

A typical test is just a simple function that executes some logic, and asserts some conditions:

```python
def test_myapp_debug():
    # ensure debug is false by default
    with MyTestApp() as app:
        assert app.debug is False
        
    # ensure debug option toggles app.debug
    with MyTestApp(argv=['--debug']) as app:
        assert app.debug is True
```

In the above example, we tested two conditions (whether `--debug` was passed or not), and asserted those conditions. If either assertion failed, the test would fail.

## Running Tests

Running tests with Pytest can be done by running `pytest` in the root of your project directory. That said, to add support for coverage.py you likely want to create a helper script (Makefile, Fabric, etc) to execute all the things at once.

Cement `generate project` includes support for `coverage.py` out of the box, and also includes a `Makefile` with helpers:

_myapp/Makefile:_

```
test: 
    python -m pytest
        -v
        --cov=myapp
        --cov-report=term
        --cov-report=html:coverage-report
        tests/
```

And this is run by:

```
make test
```

{% hint style="info" %}
The above example generates an HTML coverage report in `./coverage-report/index.html`. Open that file in your local browser to review code that is not getting hit by tests, and write tests to touch that code for better coverage.

Note that `--cov=myapp` is telling coverate to only report on your code, rather than including all other libraries your code has imported/executed.
{% endhint %}
