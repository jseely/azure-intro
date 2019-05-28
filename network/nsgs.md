Network Security Groups
=======================
Security in a virtual network is managed at the subnet and NIC level through
[Network Security Groups](https://azure.microsoft.com/en-us/documentation/articles/virtual-networks-nsg/), or nsgs.  An nsg works like a firewall: it comprises
a collection of ACL rules, each of which matches traffic against a pattern and
allows or denies it.  Inbound traffic passes through an nsg bound to the subnet
first, then through the nsg bound to the receiving NIC.  The reverse is true
for outbound traffic.

While a single nsg can be bound to any number of subnets and/or nics, 
each subnet or nic can be bound to only a single nsg.  This ability to reuse 
nsgs is important in large deployments, because the default quota for them is
100 per subscription per region.

```bash
# az network nsg create -g <resource-group> -n <name> -l <location>

$ az network nsg create -g intro-rg -n intro-nsg -l westus
```

The default rules shown above allow inbound traffic from within the vnet
or from an attached [load balancer](lbs.md), but deny from all other sources.
Rules are checked in order of priority, and terminate with the first
matching rule.

We can extend the rules, for example allowing ssh (tcp port 22) like this:
```bash
# az network nsg rule create -g <resource-group> --nsg-name <nsg-name> -n allow-ssh-in --access Allow/Deny --direction Inbound/Outbound --protocol Tcp/Udp/* --source-port-ranges <port> --priority <priority>

$ az network nsg rule create -g intro-rg --nsg-name intro-nsg -n allow-ssh-in --access Allow --direction Inbound --protocol Tcp --source-port-ranges 22 --priority 100

{
  "access": "Allow",
  "description": null,
  "destinationAddressPrefix": "*",
  "destinationAddressPrefixes": [],
  "destinationApplicationSecurityGroups": null,
  "destinationPortRange": "80",
  "destinationPortRanges": [],
  "direction": "Inbound",
  "etag": "W/\"509ecf2b-ddab-403f-b44f-405891b3e99d\"",
  "id": "/subscriptions/f9508c82-cb83-4a04-824a-30a326257ebd/resourceGroups/intro-rg/providers/Microsoft.Network/networkSecurityGroups/intro-nsg/securityRules/allow-ssh-in",
  "name": "allow-ssh-in",
  "priority": 100,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "intro-rg",
  "sourceAddressPrefix": "*",
  "sourceAddressPrefixes": [],
  "sourceApplicationSecurityGroups": null,
  "sourcePortRange": "22",
  "sourcePortRanges": [],
  "type": "Microsoft.Network/networkSecurityGroups/securityRules"
}
```

Showing the NSG or listing the rules will verify that the rule has been added:

```bash
$ az network nsg show -g intro-rg -n intro-nsg
```

```bash
$ az network nsg rule list -g intro-rg --nsg-name intro-nsg
```

Once the NSG has been created, it can be applied to a subnet or NIC.

```bash
$ az network nic update -g intro-rg -n intro-nic --network-security-group intro-nsg
```

