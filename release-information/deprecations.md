# Deprecations

## Enabling Deprecation Warnings

Deprecation warnings are handled by the same warning system as Python using the `warnings` library. To enable warning, set the [`PYTHONWARNINGS`](https://docs.python.org/3/using/cmdline.html#envvar-PYTHONWARNINGS) environment variable.

```
export PYTHONWARNINGS=once
```

{% hint style="info" %}
Python warnings should always be enabled for unit tests. Pytest, the default testing framework for Cement generated projects, enables this by default.
{% endhint %}

## Cement v3.0.10

### 3.0.10-1

The logging facility `FATAL` is being deprecated in favor of `CRITICAL`. This follows the standarding library upstream:

* [https://docs.python.org/3/library/logging.html#logging-levels](https://docs.python.org/3/library/logging.html#logging-levels)

Though the FATAL facility [may never be fully deprecated](https://bugs.python.org/issue40836) upstream, it makes sense to deprecate it in Cement.

{% hint style="warning" %}
Support for the `FATAL` facility, and `app.log.fatal()` may be removed any time in or after Cement v3.2.0.
{% endhint %}

##

## Cement v3.0.8

### 3.0.8-1

The environment variable `CEMENT_FRAMEWORK_LOGGING` is being deprecated in favor of [`CEMENT_LOG`](../environment-variables.md#cement\_log).

**Related:**

* [https://github.com/datafolklabs/cement/issues/638](https://github.com/datafolklabs/cement/issues/638)

**Usage:**

```
export CEMENT_LOG=1

myapp {command, options, etc}
```

Setting `CEMENT_LOG=1` will set `App.Meta.framework_logging = True`.

{% hint style="warning" %}
Support for `CEMENT_FRAMEWORK_LOGGING` will be removed in Cement v3.2.0.
{% endhint %}

### 3.0.8-2

In Cement v3.0.x, the default for [`App.Meta.framework_logging`](https://cement.readthedocs.io/en/3.0/api/core/foundation/#cement.core.foundation.App.Meta.framework\_logging) is `True`, however framework logging is only triggered if the `--debug` option is passed at the command-line. The `--debug` option was previously hard-coded, but is now configurable and therefore should no longer be used to toggle framework logging. &#x20;

In Cement 3.2.0, the logic of `App.Meta.framework_logging` will be repurposed, or removed.  The plan, currently, is that `--debug` will only toggle the logging level and `App.Meta.debug` but not toggle Cement framework logging. One thought is that `App.Meta.framework_logging` could trigger framework/extensions to use the App logger, once it is available (instead of MinimalLogger everywhere).&#x20;

As of 3.0.8, you can use `CEMENT_LOG=1` (environment variable) instead of `--debug` for the same functionality.

FIXME: Exact details to be determined.

Related:

* [https://github.com/datafolklabs/cement/issues/612](https://github.com/datafolklabs/cement/issues/612)
* [https://github.com/datafolklabs/cement/issues/613](https://github.com/datafolklabs/cement/issues/613)



