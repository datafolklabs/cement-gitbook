# Scrub



The Scrub Extension provides a easy mechanism for obfuscating sensitive information from command line output. Useful for debugging, and providing end-user output to developers without including sensitive info like IP addresses, phone numbers, credit card numbers, etc.

Scrubbing happens in a `post_render` hook but iterating over the list of tuples in `App.Meta.scrub`and calling `re.sub()` on the text provided by the output handler in use. Therefore, all output produced by `app.render()` will be scrubbed… including JSON, YAML, or any other output handler.

**Requirements**

> * No external dependencies.

**Configuration**

This extension does not honor any application configuration settings.

**App Meta Data**

This extension honors the following `App.Meta` options:

* **scrub**: A `list` of `tuples` in the form of: `[ ('REGEX', 'REPLACEMENT-STRING') ]`
* **scrub\_argument**: The list of args to use when adding the scrub option to the parser. Default: `['--scrub']`
* **scrub\_argument\_help**: The help text to use when adding the scrub option to the parser. Default: `'obfuscate sensitive data from output'`

**Example**

```text
from cement import App

class MyApp(App):
    class Meta:
        label = 'myapp'
        extensions = ['scrub', 'print']
        scrub = [
            ### obfuscate ipv4 addresses
            (r"\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}", '***.***.***.***'),
        ]


with MyApp() as app:
    app.run()

    ### use the print extension, but any output handler works

    app.print('This is an IPv4 Address: 192.168.1.100')
```

Looks like:

```text
$ python myapp.py
This is an IPv4 Address: 192.168.1.100

$ python myapp.py --scrub
This is an IPv4 Address: ***.***.***.***
```

