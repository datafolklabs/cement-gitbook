# Caching



Cement defines a cache interface called [ICache](https://docs.builtoncement.com/%7B%7B%20version%20%7D%7D/api/core/cache.html#cement.core.cache.ICache), but does not implement caching by default. 

{% hint style="warning" %}
Cement often includes multiple handler implementations of an interface that may or may not have additional features or functionality than the interface requires.  The documentation below only references usage based on the interface and default handler \(not the full capabilities of an implementation\).
{% endhint %}

**Cache Handlers Included with Cement:**

* ​[MemcachedCacheHandler](https://docs.builtoncement.com/%7B%7B%20version%20%7D%7D/api/ext/ext_memcached.html#cement.ext.ext_memcached.MemcachedCacheHandler)​
* ​[RedisCacheHandler](https://docs.builtoncement.com/%7B%7B%20version%20%7D%7D/api/ext/ext_redis.html#cement.ext.ext_redis.RedisCacheHandler)​

Please reference the [ICache](https://docs.builtoncement.com/%7B%7B%20version%20%7D%7D/api/core/cache.html#cement.core.cache.ICache) interface documentation for writing your own cache handler.

### General Usage {#general-usage}

For this example we use the Memcached extension, which requires the `pylibmc` library to be installed, as well as a Memcached server running on localhost.

Example:

**/path/to/myapp.conf**

```text
[myapp]extensions = memcached​[cache.memcached]# comma separated list of hosts to usehosts = 127.0.0.1​# time in millisecondsexpire_time = 300
```

**myapp.py**

```text
from cement.core.foundation import CementApp​with CementApp('myapp') as app:    # run the application    app.run()​    # set a cached value    app.cache.set('my_key', 'my value')​    # get a cached value    app.cache.get('my_key')​    # delete a cached value    app.cache.delete('my_key')​    # delete the entire cache    app.cache.purge()
```

