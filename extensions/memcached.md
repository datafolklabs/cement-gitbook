# Memcached

## Introduction

The Memcached Extension includes the [`MemcachedCacheHandler`](https://cement.readthedocs.io/en/3.0/api/ext/ext\_memcached/#cement.ext.ext\_memcached.MemcachedCacheHandler) and provides application caching and key/value store support via Memcached.

**Documentation References:**

* [Caching](../core-foundation/caching.md)

**API References:**

* [Cement Memcached Extension](https://cement.readthedocs.io/en/3.0/api/ext/ext\_memcached/)
* [Pylibmc Library](http://sendapatch.se/projects/pylibmc/)

## Requirements

* Pylibmc
  * There are known issues installing `pylibmc` on macOS/Homebrew via PIP.  [This post might be helpful](http://stackoverflow.com/questions/14803310/error-when-install-pylibmc-using-pip).

{% hint style="info" %}
Cement 3.0.8+:

`pip install cement[memcached]`
{% endhint %}

{% hint style="warning" %}
Applications using Cement <3.0.8 should continue to include `pylibmc` in their dependencies.
{% endhint %}

## Configuration

This extension honors the following config settings under a `[cache.memcached]` section in any configuration file:

| **Setting**      | **Description**                                                                                           |
| ---------------- | --------------------------------------------------------------------------------------------------------- |
| **expire\_time** | The default time in seconds to expire items in the cache.  Default: `0` (does not expire)                 |
| **hosts**        | List of memcached servers (comma separated if using a plain text based config handler like configparser). |

## Usage

{% tabs %}
{% tab title="Example: Using Memcached Cache Handler" %}
{% code title="myapp.py" %}
```python
from cement import App
from cement.utils.misc import init_defaults

CONFIG = init_defaults('myapp', 'cache.memcached')
CONFIG['cache.memcached']['expire_time'] = 300 # seconds
CONFIG['cache.memcached']['hosts'] = ['127.0.0.1']

class MyApp(App):
    class Meta:
        label = 'myapp'
        config_defaults = CONFIG
        extensions = ['memcached']
        cache_handler = 'memcached'

with MyApp() as app:
    # Run the app
    app.run()

    # Set a cached value
    app.cache.set('my_key', 'my value')

    # Get a cached value
    app.cache.get('my_key')

    # Delete a cached value
    app.cache.delete('my_key')

    # Delete the entire cache
    app.cache.purge()
```
{% endcode %}

{% code title="~/.myapp.conf" %}
```
[myapp]

# set the cache handler to use
cache_handler = memcached


[cache.memcached]

# time in seconds that an item in the cache will expire
expire_time = 3600

# comma seperated list of memcached servers
hosts = 127.0.0.1, cache.example.com
```
{% endcode %}
{% endtab %}
{% endtabs %}
