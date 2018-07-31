# Redis



The Redis Extension provides application caching and key/value store support via Redis.

### Requirements

> * redis \(`pip install redis`\)

### Configuration

This extension honors the following config settings under a `[cache.redis]` section in any configuration file:

> * **expire\_time** - The default time in second to expire items in the cache. Default: 0 \(does not expire\).
> * **host** - Redis server.
> * **port** - Redis port.
> * **db** - Redis database number.

Configurations can be passed as defaults to a `cement.App`:

```text
from cement import App
from cement.utils.misc import init_defaults

defaults = init_defaults('myapp', 'cache.redis')
defaults['cache.redis']['expire_time'] = 0
defaults['cache.redis']['host'] = '127.0.0.1'
defaults['cache.redis']['port'] = 6379
defaults['cache.redis']['db'] = 0

class MyApp(App):
    class Meta:
        label = 'myapp'
        config_defaults = defaults
        extensions = ['redis']
        cache_handler = 'redis'
```

Additionally, an application configuration file might have a section like the following:

```text
[myapp]

# set the cache handler to use
cache_handler = redis


[cache.redis]

# time in seconds that an item in the cache will expire
expire_time = 300

# Redis server
host = 127.0.0.1

# Redis port
port = 6379

# Redis database number
db = 0
```

### Usage

```text
from cement import App
from cement.utils.misc import init_defaults

defaults = init_defaults('myapp', 'redis')
defaults['cache.redis']['expire_time'] = 300 # seconds
defaults['cache.redis']['host'] = '127.0.0.1'
defaults['cache.redis']['port'] = 6379
defaults['cache.redis']['db'] = 0

class MyApp(App):
    class Meta:
        label = 'myapp'
        config_defaults = defaults
        extensions = ['redis']
        cache_handler = 'redis'

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

