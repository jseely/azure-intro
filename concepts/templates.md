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

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": []
}
```

While this template has hard-coded values that correspond to the current
state of its resources, the template can then be paramaterized to model
these as variables.  The template and associated parameter file can then
be used to [deploy](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-template-deploy-cli) similar systems of resources elsewhere.

The resource group that the template is being deployed into must already
exist.

```bash
# az group deployment create -g <resource-group> --template-file <template-file>

$ az group deployment create -g intro-rg --template-file intro-rg.json

{
  "id": "/subscriptions/f9508c82-cb83-4a04-824a-30a326257ebd/resourceGroups/intro-rg/providers/Microsoft.Resources/deployments/intro-rg",
  "location": null,
  "name": "intro-rg",
  "properties": {
    "correlationId": "60a6de84-5ad2-4a3a-92e3-1ad39d557559",
    "debugSetting": null,
    "dependencies": [],
    "duration": "PT6.6216406S",
    "mode": "Incremental",
    "onErrorDeployment": null,
    "outputResources": [],
    "outputs": null,
    "parameters": {},
    "parametersLink": null,
    "providers": [],
    "provisioningState": "Succeeded",
    "template": null,
    "templateHash": "394437604378211643",
    "templateLink": null,
    "timestamp": "2019-05-28T02:07:04.191746+00:00"
  },
  "resourceGroup": "intro-rg",
  "type": null
}
```

Export the template used for a deployment...

```bash
# az group deployment export -g <deployment> -n <resource-group>

$ az group deployment export -g intro-rg -n intro-rg

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": []
}
```

