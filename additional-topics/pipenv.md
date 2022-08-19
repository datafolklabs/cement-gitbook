# Pipenv

## Introduction to Pipenv

From the [Pipenv documentation](https://pipenv.pypa.io/en/latest/):

> **Pipenv** is a tool that aims to bring the best of all packaging worlds (bundler, composer, npm, cargo, yarn, etc.) to the Python world. _Windows is a first-class citizen, in our world._
>
> It automatically creates and manages a virtualenv for your projects, as well as adds/removes packages from your `Pipfile` as you install/uninstall packages. It also generates the ever-important `Pipfile.lock`, which is used to produce deterministic builds.

{% hint style="info" %}
Cement maintainers do not currently use Pipenv, therefore this documentation is here as a helper. Future versions of Cement may include Pipenv configurations out-of-the-box (but do not currently).
{% endhint %}

## Installing Pipenv

Pipenv can be installed via Pip:

```
pip install pipenv
```

## Using Pipenv With Cement

First install Cement, and generate a project:

```bash
pip install cement

cement generate project ./myapp
```

In the generated `./myapp` directory:

```bash
### setup requirements

pipenv install -r requirements.txt


### setup requirements for dev

pipenv install -r requirements-dev.txt --dev
```

This will create the `Pipfile` and `./Pipfile.lock`. You will then need to modify `Pipfile` to include the application console script:

```
[scripts]
myapp = "python -m myapp.main"
```

Your app can then be run under `pipenv`:

```
pipenv run myapp --help
```

{% hint style="info" %}
You can then remove the `requirements.txt` and `requirements-dev.txt` if no longer needed.
{% endhint %}
