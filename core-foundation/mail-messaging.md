# Mail Messaging

## Introduction to the Mail Interface

Cement defines a [Mail Interface](https://cement.readthedocs.io/en/2.99/api/core/mail/#cement.core.mail.MailInterface), as well as the default [DummyMailHandler](https://docs.builtoncement.com/%7B%7B%20version%20%7D%7D/api/ext/ext_dummy.html#cement.ext.ext_dummy.DummyMailHandler) that implements the interface as a placeholder but does not actually send any mail.

{% hint style="warning" %}
Cement often includes multiple handler implementations of an interface that may or may not have additional features or functionality than the interface requires.  The documentation below only references usage based on the interface and default handler \(not the full capabilities of an implementation\).
{% endhint %}

**Cement Extensions that Provide Mail Handlers:**

* [Dummy](../extensions/dummy.md) _\(default\)_
* [SMTP](../extensions/smtp.md)

**API References:**

* [Cement Core Mail Module](https://cement.readthedocs.io/en/2.99/api/core/mail)

## Working with Mail Messages

{% tabs %}
{% tab title="Example: Working with Mail Messages" %}
```python
from cement import App

with App('myapp') as app:
    app.run()
    
    # send a message using the defined mail handler
    app.mail.send("Test mail message",
                  subject='My Subject',
                  to=['me@example.com'],
                  from_addr='noreply@localhost',
                  )
```
{% endtab %}

{% tab title="cli" %}
```text
python myapp.py

=============================================================================
DUMMY MAIL MESSAGE
-----------------------------------------------------------------------------

To: me@example.com
From: noreply@localhost
CC:
BCC:
Subject: My Subject

---

Test mail message

-----------------------------------------------------------------------------
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
The default `dummy` mail handler simply prints the message to console, and does not send anything.  You can override the mail handler via `App.Meta.mail_handler`, for example using the [SMTP Extension](../extensions/smtp.md).
{% endhint %}

