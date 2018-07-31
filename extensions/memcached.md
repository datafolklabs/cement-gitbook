# Memcached



The Memcached Extension provides application caching and key/value store support via Memcache.

### Requirements

> * pylibmc \(`pip install pylibmc`\)
>   * Note: There are known issues installing `pylibmc` on OSX/Homebrew via PIP. This post [might be helpful](http://stackoverflow.com/questions/14803310/error-when-install-pylibmc-using-pip).

### Configuration

This extension honors the following config settings under a `[cache.memcached]` section in any configuration file:

> * **expire\_time** - The default time in second to expire items in the cache. Default: 0 \(does not expire\).
> * **hosts** - List of Memcached servers.

Configurations can be passed as defaults to a `App`:

```text
from cement import App
from cement.utils.misc import init_defaults

defaults = init_defaults('myapp', 'cache.memcached')
defaults['cache.memcached']['expire_time'] = 0
defaults['cache.memcached']['hosts'] = ['127.0.0.1']

class MyApp(App):
    class Meta:
        label = 'myapp'
        config_defaults = defaults
        extensions = ['memcached']
        cache_handler = 'memcached'
```

Additionally, an application configuration file might have a section like the following:

```text
[myapp]

# set the cache handler to use
cache_handler = memcached


[cache.memcached]

# time in seconds that an item in the cache will expire
expire_time = 3600

# comma seperated list of memcached servers
hosts = 127.0.0.1, cache.example.com
```

### Usage

```text
from cement import App
from cement.utils.misc import init_defaults

defaults = init_defaults('myapp', 'memcached')
defaults['cache.memcached']['expire_time'] = 300 # seconds
defaults['cache.memcached']['hosts'] = ['127.0.0.1']

class MyApp(App):
    class Meta:
        label = 'myapp'
        config_defaults = defaults
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

