# Autocomplete

## Introduction to Autocomplete

Autocomplete, or autosuggest, is a common feature for many command-line applications. Most command-line users are familiar with autocomplete when they type part of a command and press `tab` to complete the rest of the command, or `tab-tab` to see a list of commands matching that prefix.&#x20;

Beyond autocomplete of the command name itself, there are ways to autocomplete sub-commands and/or options and arguments. As support for autocomplete often depends on external applications, and/or the terminal application you are using, autocomplete is a varied topic.

Cement does not currently have any mechanisms to support autocomplete out of the box. This document will serve as a helper for known ways you might find suitable for your needs.



## Fig

[Fig](https://fig.io) is "The next-generation command line" (per their site), with a suit of products that includes [IDE-Style Autocomplete for 500+ CLI tools](https://fig.io/user-manual/autocomplete).

_video from their site_

{% embed url="https://fig.io/videos/main-demo-grey.mp4" %}

Fig has built a Cement Extension that can be installed via PyPi, and included in your applications that are Built on Cement™.

See:

* [Fig Autocomplete & Cement](https://fig.io/docs/guides/integrating/integrations/cement)

## ZSH Auto Suggestions

Provides "Fish-like fast/unobtrusive autosuggestions for zsh".

See:

* [github.com/zsh-users/zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions)

## BASH Programmable Completion

BASH has mechanisms for implementing "Programmable Completion".

See:

* [BASH Manual](https://www.gnu.org/software/bash/manual/html\_node/Programmable-Completion.html)
* [github.com/scop/bash-completion](https://github.com/scop/bash-completion)
