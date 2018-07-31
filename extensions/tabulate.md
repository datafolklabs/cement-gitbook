# Tabulate



The Tabulate Extension provides output handling based on the [Tabulate](https://pypi.python.org/pypi/tabulate) library. It’s format is familiar to users of MySQL, Postgres, etc.

### Requirements

> * Tabulate \(`pip install tabulate`\)

### Configuration

This extension does not support any configuration settings.

### Usage

```text
from cement import App

class MyApp(App):
    class Meta:
        label = 'myapp'
        extensions = ['tabulate']
        output_handler = 'tabulate'

with MyApp() as app:
    app.run()

    # create a dataset
    headers = ['NAME', 'AGE', 'ADDRESS']
    data = [
        ["Krystin Bartoletti", 47, "PSC 7591, Box 425, APO AP 68379"],
        ["Cris Hegan", 54, "322 Reubin Islands, Leylabury, NC 34388"],
        ["George Champlin", 25, "Unit 6559, Box 124, DPO AA 25518"],
        ]

    app.render(data, headers=headers)
```

Looks like:

```text
| NAME               | AGE | ADDRESS                                 |
|--------------------+-----+-----------------------------------------|
| Krystin Bartoletti |  47 | PSC 7591, Box 425, APO AP 68379         |
| Cris Hegan         |  54 | 322 Reubin Islands, Leylabury, NC 34388 |
| George Champlin    |  25 | Unit 6559, Box 124, DPO AA 25518        |
```

