Virtual Networks & Subnets
==========================
An Azure [virtual network](https://azure.microsoft.com/en-us/documentation/articles/virtual-networks-overview/)
(or "vnet") is a resource that provides a logical IP communication space for
other resources.  It comprises one or more IPv4 address spaces.

By default, vnets are independent of each other and the public Internet - you
can have the same or overlapping address space in two different vnets, or in 
a vnet and the public Internet.  Even if the address space in a vnet is 
nominally in routable IP space, by default it will not be reachable from the 
public Internet.

If vnets are interconnected, or to on-premises networks, however,
the address spaces cannot intersect each other, because of routing constraints.

Note that like any other resource, vnets must be created in a region,
and cannot span regions.

```bash
# az network vnet create -g <resource-group> -n <name> --address-prefix <address-space-csv>

$ az network vnet create -g intro-rg -n intro-vnet --address-prefix 10.0.0.0/8 192.168.0.0/16
```

```bash
# az network vnet list -g <resource-group>

$ az network vnet list -g intro-rg
```

```bash
# az network vnet show -g <resource-group> -n <vnet-name>

$ az network vnet show -g intro-rg -n intro-vnet
```

## Subnets

Virtual networks are themselved composed of subnets, which each have a single
address space expressable in CIDR notation that must be within that defined
by its parent vnet.  Network resources always exist within a subnet, rather
than the parent vnet itself.

```bash
# az network vnet create -g <resource-group> -n <vnet-name> --address-prefix <address-space> --subnet-name <subnet-name> --subnet-prefix <address-space>

$ az network vnet create -g intro-rg -n intro-vnet --address-prefix 10.0.0.0/16 --subnet-name intro-subnet --subnet-prefix 10.0.0.0/24
```

```bash
$ az network vnet create -g intro-rg -n intro-vnet --address-prefix 192.168.0.0/16 --subnet-name intro-subnet2 --subnet-prefix 192.168.0.0/24
```

```bash
# az network vnet subnet list -g <resource-group-name> --vnet-name <vnet-name>

$ az network vnet subnet list -g intro-rg --vnet-name intro-vnet
```

```bash
# az network vnet subnet show -g <resource-group-name> --vnet-name <vnet-name> -n <subnet-name>

$ az network vnet subnet show -g intro-rg --vnet-name intro-vnet -n intro-subnet
```

There is a special subnet that does not exist by default, called 
"GatewaySubnet", that holds a vnet's [VPN Gateways](gateways.md).
