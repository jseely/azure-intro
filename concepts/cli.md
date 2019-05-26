Cross-Platform (xPlat) CLI
==========================

## Logging In

Before you can begin interacting with Azure through the CLI, you will have to
[login](auth.md).

```bash
$ az login
info:    Executing command login
info:    To sign in, use a web browser to open the page https://aka.ms/devicelogin and enter the code XXXXXXXX to authenticate.
```

For now, we just need to be logged in.  More details will be presented a 
little later, when we talk about [authentication](auth.md).

CLI configuration is preserved in ~/.azure/config.json.

## Syntax

The CLI syntax is generally of the form

```bash
$ az <noun-phrase> <verb> <arguments-list>
```

The noun-phrase is one or more ordered words that uniquely describe the 
particular Azure feature to be used.

Most features support the following standard verbs:
* list
* create
* show
* set
* delete

Some features, though, introduce their own verbs that are feature-specific.

The arguments list can usually be supplied in two different styles.  In
the positionally-dependent style, the optional arguments appear first in
the list, followed by the mandatory arguments in a prescribed order.
I prefer the explicit-argument style, where all of the arguments are preceded
by switches telling the CLI what parameters they apply to.  In this case,
order is not important.

## Context-Sensitive Help

You can add the "-h" or "--help" switch to get context-sensitive help.  Added
to the noun phrase, it will give you additional options for extending it.
Added after the verb, it will give full usage information, including listing
all of the parameters.

Example 

```bash
$ az vm list-usage --location westus
```
