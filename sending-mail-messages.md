# Sending Mail Messages

Cement defines a mail interface called [IMail](/%7B%7B%20version%20%7D%7D/api/core/mail.html#cement.core.mail.IMail), as well as the default [DummyMailHandler](/%7B%7B%20version%20%7D%7D/api/ext/ext_dummy.html#cement.ext.ext_dummy.DummyMailHandler) that implements the interface.

Please note that there are other handlers that implement the `IMail` interface. The documentation below only references usage based on the interface and not the full capabilities of the implementation.

The following mail handlers are included and maintained with Cement:

* [DummyMailHandler](/%7B%7B%20version%20%7D%7D/api/ext/ext_dummy.html#cement.ext.ext_dummy.DummyMailHandler) _\(default\)_
* [SMTPMailHandler](/%7B%7B%20version%20%7D%7D/api/ext/ext_smtp#cement.ext.ext_smtp.SMTPMailHandler)

Please reference the [IMail](/%7B%7B%20version%20%7D%7D/api/core/mail.html#cement.core.mail.IMail) interface documentation for writing your own mail handler.

### Example Usage

```python
from cement.core.foundation import CementApp

class MyApp(CementApp):
    class Meta:
        label = 'myapp'

with MyApp() as app:
    app.run()

    # send an email message
    app.mail.send('This is my message',
        subject='This is my subject',
        to=['you@example.com'],
        from_addr='me@example.com',
        cc=['him@example.com', 'her@example.com'],
        bcc=['boss@example.com']
        )
```

Note that the default mail handler simply prints messages to the screen, and does not actually send anything. You can override this pretty easily without changing any code by using the built-in [SMTPMailHandler](/%7B%7B%20version%20%7D%7D/api/ext/ext_smtp#cement.ext.ext_smtp.SMTPMailHandler). Simply modify the application configuration to something like:

**myapp.conf**

```text
[myapp]
mail_handler = smtp

[mail.smtp]
ssl = 1
tls = 1
auth = 1
username = john.doe
password = oober_secure_password
```

