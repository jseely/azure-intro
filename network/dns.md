Azure DNS Zones
===============
Azure DNS lets you manage [zones and records](https://docs.microsoft.com/en-us/azure/dns/dns-zones-records).

First, we need to [create a zone](https://docs.microsoft.com/en-us/azure/dns/dns-getstarted-create-dnszone-cli).  Note that because DNS is a global service, 
unlike most other resources, you do not need to specify a location for the zone.

```bash
$ az network dns zone create -g intro-rg -n intro.on-azure.info
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
