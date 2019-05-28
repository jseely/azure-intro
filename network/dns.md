Azure DNS Zones
===============
Azure DNS lets you manage [zones and records](https://docs.microsoft.com/en-us/azure/dns/dns-zones-records).

First, we need to [create a zone](https://docs.microsoft.com/en-us/azure/dns/dns-getstarted-create-dnszone-cli).  Note that because DNS is a global service, 
unlike most other resources, you do not need to specify a location for the zone.

```bash
$ az network dns zone create -g intro-rg -n intro.on-azure.info

{
  "etag": "00000002-0000-0000-ffff-d8e50915d501",
  "id": "/subscriptions/f9508c82-cb83-4a04-824a-30a326257ebd/resourceGroups/intro-rg/providers/Microsoft.Network/dnszones/intro.on-azure.info",
  "location": "global",
  "maxNumberOfRecordSets": 10000,
  "name": "intro.on-azure.info",
  "nameServers": [
    "ns1-09.azure-dns.com.",
    "ns2-09.azure-dns.net.",
    "ns3-09.azure-dns.org.",
    "ns4-09.azure-dns.info."
  ],
  "numberOfRecordSets": 2,
  "registrationVirtualNetworks": null,
  "resolutionVirtualNetworks": null,
  "resourceGroup": "intro-rg",
  "tags": {},
  "type": "Microsoft.Network/dnszones",
  "zoneType": "Public"
}
```

Once the zone has been created, you can see that the NS and SOA record sets
are created by default:

```bash
# azure network dns record-set list -g <resource-group> -z <zone>

$ az network dns record-set list -g intro-rg -z intro.on-azure.info
```

To add DNS records to our zone, we must first [create a new record set](https://docs.microsoft.com/en-us/azure/dns/dns-getstarted-create-recordset-cli):

```bash
# azure network dns record-set create -g <resource-group> -z <zone> -n <dns-name> -y <record-type>

$ az network dns record-set a create -g intro-rg -z intro.on-azure.info -n www

{
  "etag": "da374c2e-a7d8-4690-989d-d894ab30fc09",
  "fqdn": "www.intro.on-azure.info.",
  "id": "/subscriptions/f9508c82-cb83-4a04-824a-30a326257ebd/resourceGroups/intro-rg/providers/Microsoft.Network/dnszones/intro.on-azure.info/A/www",
  "metadata": null,
  "name": "www",
  "provisioningState": "Succeeded",
  "resourceGroup": "intro-rg",
  "targetResource": {
    "id": null
  },
  "ttl": 3600,
  "type": "Microsoft.Network/dnszones/A"
}
```

We can then add a record to this set.  In this case, we'll use the PIP of 
the load balancer that we created earlier.

```bash
$ az network dns record-set a add-record -g intro-rg -z intro.on-azure.info -n www -a 104.45.233.180
```

Of course, your TLD (in this case, on-azure.info) will have to be updated to delegate to the new subdomain, as well.

Other DNS Notes
===============
When you create (or later, set) a public-ip, you can use the -d switch to
specify the domain-label.  The IP will then be reachable through the FQDN
domain-label.location.cloudapp.azure.com.

Within their vnet, VMs are resolvable by their resource name.
