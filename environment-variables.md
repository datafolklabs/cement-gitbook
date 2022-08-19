# Environment Variables

## Cement Environment Variables

### CEMENT\_LOG

Parsed by the core framework, and extensions early on in runtime (before `App` is fully loaded). Triggers low-level framework logging, and toggles `App.Meta.framework_logging`.

Values:&#x20;

* `0`
* `1`

Usage:

```
export CEMENT_LOG=1
```

{% hint style="info" %}
This variable was introduced in Cement v3.0.8, and deprecates the previous setting [`CEMENT_FRAMEWORK_LOGGING`](release-information/deprecations.md#3.0.8-1).
{% endhint %}

### CEMENT\_FRAMEWORK\_LOGGING

Deprecated in 3.0.8 in favor of [CEMENT\_LOG](environment-variables.md#cement\_log) (same usage).

## Overriding App Configuration

Out of the box, Cement supports overriding application configuration settings by environment variables.

Configuration:

```yaml
myapp:
  foo: bar
```

The above configuration would be overridden by the `$MYAPP_FOO` environment variable.

```
export MYAPP_FOO=not-bar
```
