Environments
============
"Azure" is not one cloud, but many.  Most people will be using the Azure
Public Cloud, which is generally available around the world.
However, because of issues such as data sovereignty and national security,
"Azure" also comprises several sovereign clouds, each operating solely
within their respective national boundaries.

Microsoft is also introducing [Azure Stack](https://azure.microsoft.com/en-us/overview/azure-stack/), in which the underlying cloud
software fabric can be run on private hardware within enterprise data
centers.  This makes possible the creation of arbitrary commercial Azure
clouds.

Each of these clouds must support being wholly self-contained.  Everything
from REST endpoints for [resource providers](resources.md) to
[AAD tenants](auth.md) must be provided within the cloud.  Each of these
clouds is referred to generically as an "environment".

Note that because each environment is under local control, not all [resource
providers](resources.md) may be available, API versions may vary, the
[Marketplace](https://azure.microsoft.com/en-us/marketplace/) may contain
different offerings, etc.

The list of Subscription can be seen like this:

```bash
$ az account list
```

You can obtain the technical details for a given Subscription as follows:

```bash

$ az account show

{
  "environmentName": "AzureCloud",
  "id": "f2208c82-cb83-4a04-824a-30a326257asd",
  "isDefault": true,
  "name": "PGTM-pbopardikar",
  "state": "Enabled",
  "tenantId": "12345f74-371f-4db2-9a50-c62a6877a0c9",
  "user": {
    "name": "pbopardikar@pivotal.io",
    "type": "user"
  }
}
```
