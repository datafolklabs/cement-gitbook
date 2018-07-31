# SMTP



The SMTP Extension provides the ability for applications to send email based on the [smtplib](http://docs.python.org/dev/library/smtplib.html)standard library.

### Requirements

* No external depencies

### Configuration

This extension honors the following configuration settings:

> * **to** - Default `to` addresses \(list, or comma separated depending on the ConfigHandler in use\)
> * **from\_addr** - Default `from_addr` address
> * **cc** - Default `cc` addresses \(list, or comma separated depending on the ConfigHandler in use\)
> * **bcc** - Default `bcc` addresses \(list, or comma separated depending on the ConfigHandler in use\)
> * **subject** - Default `subject`
> * **subject\_prefix** - Additional string to prepend to the `subject`
> * **host** - The SMTP host server
> * **port** - The SMTP host server port
> * **timeout** - The timeout in seconds before terminating a connection
> * **ssl** - Whether to initiate SSL or not
> * **tls** - Whether to use TLS or not \(requires SSL\)
> * **auth** - Whether or not to initiate SMTP authentication
> * **username** - SMTP authentication username
> * **password** - SMTP authentication password

You can add these to any application configuration file under a `[mail.smtp]` section, for example:

**~/.myapp.conf**

```text
[myapp]

# set the mail handler to use
mail_handler = smtp


[mail.smtp]

# default to addresses (comma separated list)
to = me@example.com

# default from address
from = someone_else@example.com

# default cc addresses (comma separated list)
cc = jane@example.com, rita@example.com

# default bcc addresses (comma separated list)
bcc = blackhole@example.com, someone_else@example.com

# default subject
subject = This is The Default Subject

# additional prefix to prepend to the subject
subject_prefix = MY PREFIX >

# smtp host server
host = localhost

# smtp host port
port = 465

# timeout in seconds
timeout = 30

# whether or not to establish an ssl connection
ssl = 1

# whether or not to use start tls
tls = 1

# whether or not to initiate smtp auth
auth = 1

# smtp auth username
username = john.doe

# smtp auth password
password = oober_secure_password
```

### Usage

```text
from cement import App

class MyApp(App):
    class Meta:
        label = 'myapp'
        mail_handler = 'smtp'

with MyApp() as app:
    app.mail.send('This is my fake message',
        subject='This is my subject',
        to=['john@example.com', 'rita@example.com'],
        from_addr='me@example.com',
        )
```

