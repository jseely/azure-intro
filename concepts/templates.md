ARM Templates
=============
A set of [Azure resources](resources.md) and their configured state can be
represented in the form of a JSON file called a template.  Templates are
declarative rather than imperative - in other words, they describe *what*
the resources should look like, rather than *how* to make them so.

To retrieve a template that models the current state of an existing resource
group, we use the azure CLI as follows:

```bash
# az group export -n <resource-group> > <json output file name>

$ az group export -n intro-rg > intro-rg.json
```

While this template has hard-coded values that correspond to the current
state of its resources, the template can then be paramaterized to model
these as variables.  The template and associated parameter file can then
be used to [deploy](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-template-deploy-cli) similar systems of resources elsewhere.

The resource group that the template is being deployed into must already
exist.

```bash
# az group deployment create -g <resource-group> --template-file <template-file>

$ az group deployment create -g intro-rg-2 --template-file intro-rg.json
```

Export the template used for a deployment...

```bash
# az group deployment export -g <deployment> -n <resource-group>

$ az group deployment export -g intro-rg-2 -n intro-rg
```

