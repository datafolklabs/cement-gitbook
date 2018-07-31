# Logging



The Logging Extension provides log handling based on the standard [`logging.Logger`](https://docs.python.org/3.6/library/logging.html#logging.Logger), and is the default log handler used by Cement.

### Requirements

> * No external dependencies.

### Configuration

This extension honors the following configuration settings from the config section `log.logging`:

> * level
> * file
> * to\_console
> * rotate
> * max\_bytes
> * max\_files

A sample config section \(in any config file\) might look like:

```text
[log.logging]
file = /path/to/config/file
level = info
to_console = true
rotate = true
max_bytes = 512000
max_files = 4
```

### Usage

```text
from cement import App

with App('myapp') as app:
    app.log.info("This is an info message")
    app.log.warning("This is an warning message")
    app.log.error("This is an error message")
    app.log.fatal("This is a fatal message")
    app.log.debug("This is a debug message")
```

### Toggle Log Level Via Commandline Argument

This extension can optionally add a commandline argument to toggle the log level on the fly:

```text
$ python myapp.py --help
usage: myapp [-h] [--debug] [--quiet] [-l {info,warning,error,debug,fatal}]

optional arguments:
  -h, --help            show this help message and exit
  --debug               full application debug mode
  --quiet               suppress all output
  -l {info,warning,error,debug,fatal}
                        logging level


$ python myapp.py
INFO: This is an info message
WARNING: This is an warning message
ERROR: This is an error message
CRITICAL: This is a fatal message

$ python myapp.py -l error
ERROR: This is an error message
CRITICAL: This is a fatal message
```

**Enabling The Commandline Argument**

The argument is not enable by default for one specific reason; the log level will not be modified until **after** argument parsing happens. This can lead to a lot of confusion for developers who might not see their debug logs from a `pre_setup` hook, or anyhting that happens before argument parsing completes. For this reason, you should use this feature with caution and is why we disable it by default.

To enable the command line argument, you can disable it by setting the The command line argument can be enabled via `App.Meta.meta_defaults` for the `log.logging` handler:

```text
from cement import App, init_defaults

meta = init_defaults('log.logging')
meta['log.logging']['log_level_argument'] = ['-l', '--level']

class MyApp(App):
    class Meta:
        label = 'myapp'
        meta_defaults = meta
```

