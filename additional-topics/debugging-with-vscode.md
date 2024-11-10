# Debugging with VSCode

{% hint style="warning" %}
The following documentation has been adding by [community submission](https://github.com/datafolklabs/cement/issues/738), and has not yet been fully verified/tested.  Replace `<myapp>` with the Python module name of your application.
{% endhint %}

The following are notes on how to setup debugging in VSCode for applications Built on Cement. It has been tested on Cement 3.x on Windows 11.

### Implementation Steps

* Run > Open Configurations
* Run > Add Configurations
  * Select Python Debugger > Module > `<myapp>.main` &#x20;

Modify the configuration code block to allow for arguments/options:

_launch.json_

```json
"configurations": [
  {
    "name": "Python Debugger: Cement CLI Tool",
    "type": "debugpy",
    "request": "launch",
    "module": "<myapp>.main"
    "console": "integratedTerminal",
    "args": "${command:pickArgs}"
  }
]
```

The above configuration will prompt for arguments/options when Debug runs. An alternative approach might be to hard-code the arguments/options if you are frequently debugging a specific action and do not want to add it every time.

```json
"args": [ "list", "-h" ]
```

