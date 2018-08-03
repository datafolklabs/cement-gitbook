# Mail Messaging



Cement defines a mail interface called [IMail](https://docs.builtoncement.com/%7B%7B%20version%20%7D%7D/api/core/mail.html#cement.core.mail.IMail), as well as the default [DummyMailHandler](https://docs.builtoncement.com/%7B%7B%20version%20%7D%7D/api/ext/ext_dummy.html#cement.ext.ext_dummy.DummyMailHandler) that implements the interface.

{% hint style="warning" %}
Cement often includes multiple handler implementations of an interface that may or may not have additional features or functionality than the interface requires.  The documentation below only references usage based on the interface and default handler \(not the full capabilities of an implementation\).
{% endhint %}

**Mail Handlers Included with Cement:**

* ​[DummyMailHandler](https://docs.builtoncement.com/%7B%7B%20version%20%7D%7D/api/ext/ext_dummy.html#cement.ext.ext_dummy.DummyMailHandler) _\(default\)_
* ​[SMTPMailHandler](https://docs.builtoncement.com/%7B%7B%20version%20%7D%7D/api/ext/ext_smtp#cement.ext.ext_smtp.SMTPMailHandler)​

Please reference the [IMail](https://docs.builtoncement.com/%7B%7B%20version%20%7D%7D/api/core/mail.html#cement.core.mail.IMail) interface documentation for writing your own mail handler.

### Example Usage {#example-usage}

```text
from cement.core.foundation import CementApp​class MyApp(CementApp):    class Meta:        label = 'myapp'​with MyApp() as app:    app.run()​    # send an email message    app.mail.send('This is my message',        subject='This is my subject',        to=['you@example.com'],        from_addr='me@example.com',        cc=['him@example.com', 'her@example.com'],        bcc=['boss@example.com']        )
```

Note that the default mail handler simply prints messages to the screen, and does not actually send anything. You can override this pretty easily without changing any code by using the built-in [SMTPMailHandler](https://docs.builtoncement.com/%7B%7B%20version%20%7D%7D/api/ext/ext_smtp#cement.ext.ext_smtp.SMTPMailHandler). Simply modify the application configuration to something like:

**myapp.conf**

```text
[myapp]mail_handler = smtp​[mail.smtp]ssl = 1tls = 1auth = 1username = john.doepassword = oober_secure_password
```

