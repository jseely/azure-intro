IP Addresses
============

## [Private vs. Public IPs](https://azure.microsoft.com/en-us/documentation/articles/virtual-network-ip-addresses-overview-arm/)

Because Azure virtual networks are logicially isolated, their apparent
address space is not directly publicly routable, even if it is not one of the
RFC1918 spaces (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).  For this reason
addresses on these networks are refererred to as _private_ IP addresses.  When
a private IP address is required, almost any address that is within the
network/subnet space may be be specified.  (Azure reserves the first and last,
plus three more.  Virtual networks do not support multicast or broadcast
addressing.)  These are assigned dynamically via DHCP, so these are called
Dynamic IPs or _DIPs_.

If the DIPs are not externally routable, how can they [commnicate with the
public Internet](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-outbound-connections)?  Via a _public_ IP address, of course.  These
addresses are not part of a virtual network, even if the public IP address
were to fall within an address space assigned to a vnet.  Public IPs are
allocated from a pool maintained within Azure - you cannot specify a 
particular address.

When a DIP initiates a connection to an external address, it
undergoes source network address translation (SNAT) within Azure.  If it
is not behind a [load balancer](lbs.md), it is mapped
to a temporary public IP address - a virtual IP address, or _VIP_, 
borrowed from its host.  A DIP that is behind an Internet-facing load 
balancer is SNATted to the PIP of the load balancer.

Another option is to request a more lasting assignment of a public IP address
from the Azure pool.  These instance-level public IPs (_ILPIP_ or just _PIP_),
unlike DIPs or VIPs, are represented as resources that you must
allocate and manage.

## Dynamic vs. Static IPs

By default, both private and public IP addresses are allocated dynamically
and can change periodically (they are allocated when a resource is started,
and released when a resource is stopped or deleted).  To specify that an
address is static, use the -a switch.  For private IP addresses, the
argument to -a is the address itself.  For public IP addresses, the argument
to -a is the allocation method: "Dynamic" (the default) or "Static".

```bash
# az network public-ip create -g <resource-group> -n <name> --dns-name <dns-name> -l <region-name> --allocation-method Static|Dynamic

$ az network public-ip create -g intro-rg -n intro-pip --dns-name intro-pip-label -l westus --allocation-method Static

{
  "publicIp": {
    "ddosSettings": null,
    "dnsSettings": {
      "domainNameLabel": "intro-pip-label",
      "fqdn": "intro-pip-label.westus.cloudapp.azure.com",
      "reverseFqdn": null
    },
    "etag": "W/\"a15700cf-a5b8-432d-9a1a-08f3abcda590\"",
    "id": "/subscriptions/f9508c82-cb83-4a04-824a-30a326257ebd/resourceGroups/intro-rg/providers/Microsoft.Network/publicIPAddresses/intro-pip",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.118.162.188",
    "ipConfiguration": null,
    "ipTags": [],
    "location": "westus",
    "name": "intro-pip",
    "provisioningState": "Succeeded",
    "publicIpAddressVersion": "IPv4",
    "publicIpAllocationMethod": "Static",
    "publicIpPrefix": null,
    "resourceGroup": "intro-rg",
    "resourceGuid": "d91294bc-3888-46f6-9d65-38d26038d4dd",
    "sku": {
      "name": "Basic",
      "tier": "Regional"
    },
    "tags": null,
    "type": "Microsoft.Network/publicIPAddresses",
    "zones": null
  }
}
```

We'll need another PIP later for our load balancer, so we'll create that now
as well.

```bash
$ az network public-ip create -g intro-rg -n intro-pip-lb --dns-name intro-pip-lb-label -l westus --allocation-method Static
```

```bash
# az network public-ip list -g <resource-group-name>

$ az network public-ip list -g intro-rg

[
  {
    "ddosSettings": null,
    "dnsSettings": {
      "domainNameLabel": "intro-pip-label",
      "fqdn": "intro-pip-label.westus.cloudapp.azure.com",
      "reverseFqdn": null
    },
    "etag": "W/\"a15700cf-a5b8-432d-9a1a-08f3abcda590\"",
    "id": "/subscriptions/f9508c82-cb83-4a04-824a-30a326257ebd/resourceGroups/intro-rg/providers/Microsoft.Network/publicIPAddresses/intro-pip",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.118.162.188",
    "ipConfiguration": null,
    "ipTags": [],
    "location": "westus",
    "name": "intro-pip",
    "provisioningState": "Succeeded",
    "publicIpAddressVersion": "IPv4",
    "publicIpAllocationMethod": "Static",
    "publicIpPrefix": null,
    "resourceGroup": "intro-rg",
    "resourceGuid": "d91294bc-3888-46f6-9d65-38d26038d4dd",
    "sku": {
      "name": "Basic",
      "tier": "Regional"
    },
    "tags": null,
    "type": "Microsoft.Network/publicIPAddresses",
    "zones": null
  }
]
```

```bash
# az network public-ip show -g <resource-group-name> -n <public-ip-name>

$ az network public-ip show -g intro-rg -n intro-pip

{
  "ddosSettings": null,
  "dnsSettings": {
    "domainNameLabel": "intro-pip-label",
    "fqdn": "intro-pip-label.westus.cloudapp.azure.com",
    "reverseFqdn": null
  },
  "etag": "W/\"a15700cf-a5b8-432d-9a1a-08f3abcda590\"",
  "id": "/subscriptions/f9508c82-cb83-4a04-824a-30a326257ebd/resourceGroups/intro-rg/providers/Microsoft.Network/publicIPAddresses/intro-pip",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "40.118.162.188",
  "ipConfiguration": null,
  "ipTags": [],
  "location": "westus",
  "name": "intro-pip",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Static",
  "publicIpPrefix": null,
  "resourceGroup": "intro-rg",
  "resourceGuid": "d91294bc-3888-46f6-9d65-38d26038d4dd",
  "sku": {
    "name": "Basic",
    "tier": "Regional"
  },
  "tags": null,
  "type": "Microsoft.Network/publicIPAddresses",
  "zones": null
}
```
