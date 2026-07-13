# Generate

## Introduction

The Generate Extension includes the [Generate](https://cement.readthedocs.io/en/3.0/api/ext/ext\_generate/#cement.ext.ext\_generate.Generate) controller, and provides a mechanism for generating common content from template directories. An example use case would be the ability for application developers to easily generate new plugins for their application… similar in other applications such as Chef Software’s `chef generate cookbook` type utilities.

The [Cement Developer Tools](../getting-started/developer-tools.md) use this extension to generate projects, plugins, extensions, scripts, etc for developers building their applications on the framework.

**Documentation References:**

* [Templating](../core-foundation/templating.md)

**API References:**

* [Cement Generate Extension](http://cement.readthedocs.io/en/3.0/api/ext/ext\_generate/)

## **Requirements**

* pyYaml
* A valid [template handler](../core-foundation/templating.md) must be defined at the application level via [`App.Meta.template_handler`](http://cement.readthedocs.io/en/3.0/api/core/foundation/#cement.core.foundation.App.Meta.template\_handler) such as `jinja2`, `mustache`, etc.

{% hint style="info" %}
Cement 3.0.8+:

`pip install cement[generate]`
{% endhint %}

{% hint style="warning" %}
Applications using Cement <3.0.8 should continue to include `pyYaml` in their dependencies.
{% endhint %}

## **Configuration**

### **Application Configuration Settings**

This extension honors the following settings under the primary namespace (ex: `[myapp]`) of the application configuration:

| **Setting**       | **Description**                               |
| ----------------- | --------------------------------------------- |
| **template\_dir** | Directory path of a local template directory. |

### **Application Meta Options**

This extension honors the following [`App.Meta`](http://cement.readthedocs.io/en/3.0/api/core/foundation/?highlight=app.meta#cement.core.foundation.App.Meta) options:

| **Option**            | **Description**                                         |
| --------------------- | ------------------------------------------------------- |
| **template\_handler** | A template handler to use as the backend for templating |
| **template\_dirs**    | A list of data directories to look for templates        |
| **template\_module**  | A python module to look for templates                   |

## **Usage**

### **Examples**

{% tabs %}
{% tab title="Example: Using Generate Extension" %}
```python
from cement import App

class MyApp(App):
    class Meta:
        label = 'myapp'
        extensions = ['generate', 'jinja2']
        template_handler = 'jinja2'


with MyApp() as app:
    app.run()
```
{% endtab %}

{% tab title="cli" %}
```
$ python myapp.py --help
usage: myapp [-h] [--debug] [--quiet] {generate} ...

optional arguments:
  -h, --help  show this help message and exit
  --debug     toggle debug output
  --quiet     suppress all output

sub-commands:
  {generate}
    generate  generate controller


$ python myapp.py generate --help
usage: myapp generate [-h] {plugin} ...

optional arguments:
  -h, --help  show this help message and exit

sub-commands:
  {plugin}
    plugin      generate plugin from template
```
{% endtab %}
{% endtabs %}

### **Generate Templates**

The Generate Extension looks for a `generate` sub-directory in all defined template directory paths defined at the application level. If it finds a `generate` directory it treats all items within that directory as a generate template.

A Generate Template requires a single configuration YAML file called `.generate.yml` that looks something like:

```yaml
---
ignore:
    - "^(.*)ignore-this(.*)$"
    - "^(.*)ignore-that(.*)$"

exclude:
    - "^(.*)exclude-this(.*)$"
    - "^(.*)exclude-that(.*)$"

variables:
    - name: 'my_variable_name'
      prompt: 'The Prompt Displayed to The User'
```

**Generate Template Configuration**

The following configurations are supported in a generate template’s config:

| **ignore**    | A list of regular expressions to match files that you want to completely ignore                    |
| ------------- | -------------------------------------------------------------------------------------------------- |
| **exclude**   | A list of regular expressions to match files that you want to copy only (not rendered as template) |
| **variables** | A list of variable definitions that support the following sub-keys:                                |

**Variable Definition Sub-Keys**

| **Key**      | **Description**                                                                                                                            |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------ |
| **name**     | The variable name exposed to the template context (required)                                                                                |
| **prompt**   | The prompt displayed to the user. Use `prompt: false` for a silent variable that takes its `default` without prompting (requires `default`) |
| **default**  | Default value (used on empty input, and as the value for `--defaults` runs)                                                                  |
| **case**     | Optional case transform applied to the value: one of `lower`, `upper`, `title`                                                              |
| **validate** | Optional regular expression the value must match (generation aborts on mismatch)                                                            |
| **type**     | Cement 3.0.16+ — one of `string` (default), `boolean`, `choice`. See [Typed Variables](generate.md#typed-variables) below                    |
| **extend**   | Cement 3.0.16+ — list of conditional-effect rules keyed on the resolved value. See [`extend:`](generate.md#extend-conditional-effects)       |
| **requires** | Cement 3.0.16+ — gate this variable on other top-level variables. See [`requires:`](generate.md#requires-variable-gating)                    |

## Typed Variables

{% hint style="info" %}
Cement 3.0.16+
{% endhint %}

Typed variables let a generate template offer **optional features** — Y/N
toggles and multi-choice pickers that conditionally prompt for extra
variables, skip files, and gate on one another. Everything lives in the
single `variables:` list of `.generate.yml`; each entry may carry a `type:`
and optional `extend:` / `requires:` keys.

The mental model:

* A **`boolean`** or **`choice`** variable resolves to a real typed value at
  the **top level** of the template context — so `{% if docker %}`
  and `{% if web_framework == "flask" %}` work directly.
* An **`extend:`** rule fires when the resolved value matches its `when:`,
  contributing extra `variables:` (prompted in place), `ignore:` patterns
  (files skipped), and `exclude:` patterns (files copied verbatim).
* A **`requires:`** key gates a variable on other variables — if the gate
  fails, the variable is silently resolved to its `default` and none of its
  `extend:` rules fire.

### `type: string`

The classic variable — a plain `{name, prompt, default}` entry with optional
`case:` / `validate:`. Omitting `type:` is equivalent to `type: string`, so
existing templates are unaffected.

### `type: boolean`

A single y/N prompt rendered as `<prompt> [(Y)es/(N)o] [<default>]:`. With no
`prompt:` key the label defaults to `Enable <name>`. Input `y`/`yes` maps to
`True`, `n`/`no` to `False`, empty input to `default`. The resolved value is
a real Python `bool`:

```yaml
variables:
    -   name: docker
        type: boolean
        default: true
        extend:
            -   when: true
                variables:
                    -   name: python_version
                        prompt: "Python Version (for Docker)"
                        default: "3.13"
            -   when: false
                ignore:
                    - '.*Dockerfile.*'
                    - '.*\.dockerignore.*'
```

For full control of the wording and accepted tokens, give `prompt:` an object
— `accept:` / `reject:` are case-insensitive token lists that map the answer
to a `bool` (input matching neither aborts, like a failed `validate:`):

```yaml
    -   name: docker
        type: boolean
        default: true
        prompt:
            text: "Use Docker? [(Y)ay/(N)ay]"
            accept: [y, yay]
            reject: [n, nay]
```

{% hint style="warning" %}
Quote bool-like tokens (`"yes"`, `"no"`, `"on"`, `"off"`) inside `accept:` /
`reject:` — under YAML 1.1 they otherwise decode to a Python `bool` and the
loader rejects them with a clear `ValueError`.
{% endhint %}

### `type: choice`

A numbered picker. Each option is either a bare scalar or an object with
`value:` (required — the string the variable resolves to) and an optional
`prompt:` label for the numbered list:

```yaml
    -   name: web_framework
        type: choice
        prompt: "Web Framework"
        default: "none"
        options:
            -   value: "none"
                prompt: "None — no web framework"
            -   value: "flask"
                prompt: "Flask Web Framework"
            -   value: "fastapi"
                prompt: "FastAPI Web Framework"
```

`default:` is **required** and must equal one of the option values (validated
at config load). It is used on empty input and for `--defaults` runs.

### `extend:` — conditional effects

Each variable may carry an `extend:` list. A rule fires when its `when:`
matches the resolved value:

| **Match form**         | **Example**                    | **Applies to**            |
| ---------------------- | ------------------------------ | ------------------------- |
| Scalar equality        | `when: true` / `when: "flask"` | all types                 |
| In-list membership     | `when: ["flask", "fastapi"]`   | all types                 |
| Regular expression     | `when: "^3\\."`                | `string` variables only   |

A firing rule contributes:

| **Key**       | **Effect**                                                                                                     |
| ------------- | --------------------------------------------------------------------------------------------------------------- |
| **variables** | Extra variables prompted *in place*, in declaration order (use `prompt: false` for silent values like versions) |
| **ignore**    | Regex patterns for files to skip entirely                                                                        |
| **exclude**   | Regex patterns for files to copy verbatim (not rendered as templates)                                            |

Multiple matching rules compose. An explicit YAML `null` block (e.g.
`ignore:` with no items) coalesces to an empty list — it does not error.

### `requires:` — variable gating

A variable may be gated on other **top-level** variables:

```yaml
    -   name: docker_compose
        type: boolean
        default: true
        requires:
            - docker
```

Three forms are supported:

| **Form**  | **Example**                                | **Meaning**                    |
| --------- | ------------------------------------------ | ------------------------------ |
| List      | `requires: [docker]`                       | each named variable is truthy  |
| Map       | `requires: {web_framework: flask}`         | equality                       |
| Map+list  | `requires: {web_framework: [flask, fastapi]}` | in-list membership          |

Multiple entries are AND-ed and resolve order-independently — prerequisites
are resolved (and prompted) before their dependents, regardless of
declaration order. Interactive prompting is lazy: **declining a prerequisite
skips the dependent's prompt entirely.**

{% hint style="warning" %}
**Gated-out is not forced-off.** When a `requires:` gate fails, the variable
resolves to its (typed) **`default`** — it is not prompted, and none of its
`extend:` rules fire. If the dependent's `default` is `true`, its files still
render (only its `when: false` cleanup is skipped). If you want "declining
the prerequisite drops the dependent's files too", put those `ignore:`
patterns on the **prerequisite's** `when: false` rule.
{% endhint %}

A `requires:` gate can only reference other top-level variable names (nested
`extend.variables` are not addressable), and a gated variable **must** have a
`default:` — a missing default raises `ValueError`.

### CLI Invocation

```
# interactive: prompts for each variable, lazily (prerequisites first)
$ myapp generate webapp ./myproject

# headless: every variable takes its default, no prompts
$ myapp generate webapp ./myproject --defaults

# overwrite an existing destination
$ myapp generate webapp ./myproject --defaults --force

# copy the template source verbatim (no rendering, no prompts)
$ myapp generate webapp ./myproject --clone
```

### Using Values in Templates

Resolved values land at the **top level** of the template context — booleans
as real `bool`s, choices as strings, and any `extend.variables` injected by a
firing rule as ordinary top-level variables:

```django
{% if docker %}
COPY Dockerfile support files...
{% endif %}

{% if web_framework == "flask" %}
flask=={{ framework_version }}
{% endif %}
```

{% hint style="warning" %}
A boolean rendered as *text* (`{{ docker }}`) interpolates the capitalized
Python repr — `True` / `False` — not `true`/`false`. This applies to both the
jinja2 and mustache handlers. Use conditionals (`{% if docker %}`, mustache
`{{#docker}}` / `{{^docker}}`) to gate content; only direct text
interpolation shows the repr.
{% endhint %}

### Worked Example

The Cement source tree ships a complete working example under
[`demo/generate-features/`](https://github.com/datafolklabs/cement/tree/main/demo/generate-features)
— a `webapp` template combining a string variable (`project_name`), two
booleans (`docker`, and `docker_compose` which `requires: [docker]`), and a
choice (`web_framework`: none/flask/fastapi) with per-branch silent version
variables.

Generated with `--defaults` (docker on, compose on, no framework):

```
myproject/
├── .dockerignore
├── Dockerfile
├── README.md
├── app.py
└── docker-compose.yml
```

Generated with `web_framework=fastapi` (everything else default):

```
myproject/
├── .dockerignore
├── Dockerfile
├── README.md
├── app.py
├── docker-compose.yml
├── requirements.txt    # fastapi==0.115 via the silent framework_version
└── wsgi.py
```

### Authoring Checklist

1. Start with your base `variables:` (plain strings) and template files.
2. Bucket the optional content — which files/variables belong to which toggle?
3. Add a `type: boolean` (or `choice`) variable per toggle.
4. Add `when: false` (or per-choice) `ignore:` rules for files to drop —
   remembering that a dependent's cleanup belongs on its **prerequisite's**
   decline branch if it must cascade.
5. Add `extend.variables` for follow-up prompts (or silent `prompt: false`
   metadata) on the enabling branch.
6. Add `requires:` for dependencies between toggles.
7. Gate rendered content inside files with `{% if <name> %}`.
8. Test **both** paths: `--defaults` and an interactive run that declines
   each toggle.

### Pitfalls & Validation

* **`exclude` vs `ignore`** — `exclude` still copies the file (verbatim, no
  rendering); `ignore` drops it entirely.
* **Anchor your regexes loosely** — patterns match against full paths, so use
  `'.*Dockerfile.*'`, not `'Dockerfile'`.
* **Cyclic `requires`** (A requires B, B requires A) fails fast with a clear
  `ValueError` — it will not hang or recurse.
* **`requires` references variable names only** — top-level `variables:`
  entries, not arbitrary template variables or nested `extend.variables`.
* Schema violations raise `ValueError` at config load (they survive
  `python -O`, unlike assertions): a `choice` with empty/missing `options`,
  a `default` not present in `options`, option objects without `value:`,
  bool-decoded tokens in `accept:`/`reject:`, a `requires:`-gated variable
  without a `default`, and `requires:` naming an unknown variable.
