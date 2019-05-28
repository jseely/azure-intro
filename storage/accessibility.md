Accessibility
=============

In order to manipulate a storage account (as opposed to data
contained within it), you must have its [connection string](https://docs.microsoft.com/en-us/azure/storage/storage-configure-connection-string).

```bash
# az storage account show-connection-string -g <resource-group> -n <account-name>

$ az storage account show-connection-string -g intro-rg -n introdlkfj4875dfstrg

{
  "connectionString": "DefaultEndpointsProtocol=https;EndpointSuffix=core.windows.net;AccountName=introdlkfj4875dfstrg;AccountKey=AMoZRRAWmTTWcMpKXSwgxejxqRM6CVQIzwj3N096LM0gHfbnbNH9uG5c3xRDE+2MhsyWrY7hneYoD6zCbQ=="
}
```

The connection string includes an account key; this is similar to a root
password.  The storage account has both a primary and a secondary account
key.  Anyone who has one of these keys has full access to the account.  If
either key is exposed, it should be replaced immediately:

```bash
# az storage account keys renew -g <resource-group> --key <primary|secondary> -n <account-name>

$ az storage account keys renew -g intro-rg --key primary -n introdlkfj4875dfstrg
```

Instead of specifiying the storage account details on the command line, they
can also be put into environment variables.  This is convenient if you'll be
using the same storage account repeatedly.  You can either use the pair
AZURE_STORAGE_ACCOUNT and AZURE_STORAGE_ACCESS_KEY, or you can use
AZURE_STORAGE_CONNECTION_STRING.

Instead of keys, access can also be managed using [shared access signatures]
(https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-shared-access-signature-part-1).
These are more fine-grained, and provide access to one or more service
endpoints within the account, rather than to the account as a whole.

It is also possible to set public accessibility at the container or the blob
level:

```bash
# az storage container set-permission --help
```

The "Container" setting allows access to the container metadata, including the
list of blobs contained within.  To allow access to a blob only if the URL is
known, use "Blob".

At present, however, blob containers in premium storage accounts do not support
public accessibility.  

