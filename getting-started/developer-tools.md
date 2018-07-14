---
description: >-
  Introduction to the included developer tools provided that streamline the
  process of creating new projects, and/or extending existing ones.
---

# Developer Tools

## Introduction

Cement ships with a CLI utility that includes tools and helpers for application developement. It is in-itself an application Built on Cement™, and can serve as a working example of some of the key features of the framework.

See: `$ cement --help`

The Cement CLI uses the builtin `Generate Extension` in order to easily create new applications, extensions, plugins, or scripts.

See `$ cement generate --help`

## Creating Your First Application Built on Cement™

Using the Cement Developer Tools CLI, you can quickly generate a new application:

_Note: Press_ `<ENTER>` _in order to take the default values for_ `myapp`_:_

```text
$ cement generate app ./myapp
INFO: Generating cement app in ./myapp
App Label [myapp]:
App Name [My Application]:
App Class Name [MyApp]:
App Description [MyApp Does Amazing Things!]:
Creator Name [John Doe]:
Creator Email [john.doe@example.com]:
Project URL [https://github.com/johndoe/myapp/]:
License [unlicensed]:
```

We have just generated a new application in the directory `./myapp`.

