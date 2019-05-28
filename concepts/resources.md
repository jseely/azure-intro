Resources, Resource Providers, and Resource Groups
==================================================

## Resources

An [Azure resource](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview) is a discrete element of functionality that can 
be independently provisioned, managed, and released; it generates costs
according to a specific metric, according to use, independently of other
resources.  In short, whatever you're building in Azure, resources are
what you're building it with, and determine how much you'll pay for it.

Every resource in Azure is provisioned as part of a [subscription]
(subscriptions.md) within a specific [region/location](regions.md).

## Resource Providers

The entire lifecycle of a resource, and all interactions with it,
is governed by its resource provider (RP) and resource type.  A single 
RP can expose multiple resource types, but a given resource can only be
of a single type.  The provider, version, and type exclusively determine the
legal means and meaning for interactions with the resource.

RPs are implemented as [REST APIs](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-rest-api).  When a request to provision a new
resource is received, the RP returns the resource's unique id on success.
Subsequent manipulation of the resource goes through the RP's REST API,
with the id as a parameter.  Additional information associated with the
request is typically passed in through JSON in the request body.  Data
in the response is also returned as JSON.

The REST APIs for all RPs are versioned - the desired version is explicitly
specified in the call.  RP versions have their own lifecycle, arriving in
a preview state, rolled out over time to various regions, being promoted 
to GA, then chosen as the default
version by various interfaces, before being overtaken by newer versions,
deprecated, and finally obsoleted.  It is the normal case that [multiple
versions](debugging.md) of a given RP are concurrently available.

Each RP is registered in Azure with a namespace, which also uniquely
identifies it.  Because RPs can also be provided by third parties, these
namespaces are hierarchical and begin with the vendor of origin.  Thus, 
the first party RPs provided by Microsoft have namespaces such as 
Microsoft.Network, Microsoft.Storage, and Microsoft.Compute.

```bash
$ az provider list
```

For a specific provider, you can get the list of the resource types that 
it supports, and the regions where each is available.

```bash
# az provider show --namespace <provider-namespace>

$ az provider show --namespace Microsoft.Compute
```

It would be helpful to be able to list all of the resource providers
available within a given location, but the CLI doesnt support that currently.

## Resource Groups

Resources are often so fine-grained that their value comes from 
being combined with other resources, and it is helpful to organize them
accordingly.  Because these resources are conceptually
part of a single solution, they typically have the same lifecycle as well.
For these reasons, Azure allows bundling arbitrary resources into a
resource group.

A resource group is itself a resource, and like all resources, it has to
be created in a given location:

```bash
# az group create -n <resource-group> -l <location>

$ az group create -n intro-rg -l westus

{
  "id": "/subscriptions/f9508c82-cb83-4a04-824a-30a326257asd/resourceGroups/intro-rg",
  "location": "westus",
  "managedBy": null,
  "name": "intro-rg",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

However, the resource group can contain resources that are **not** in the
same region as the group itself.

You can see all of the resource groups for the current subscription with

```bash
$ az group list
```

and all of the resources within a group in the current subscription with

```bash
# az group show -n <resource-group>

$ az group show -n intro-rg
```

or

```bash
# az resource list -g <resource-group>

$ az resource list -g intro-rg
```

Finally, when you're done with all of the resources in a group, you can
delete them together by deleting the group that contains them.

```bash
# az group delete -n <resource-group>
```

These commands also take the "--subscription <subscription-id>" switch
to specify an alternate subscription.
