NICs & IP Address Binding
=========================

IP addresses can be assigned to some Azure resources directly (e.g. load
balancers).  Virtual machines, however, require that the address be assigned
to a virtual [Network Interface Card (NIC)](https://azure.microsoft.com/en-us/documentation/articles/virtual-network-network-interface-overview/), which is then assigned to the VM. *This takes place at the time of
NIC creation and cannot be changed later via the xplat CLI.*  While the CLI does
incude an 'azure network nic set' command, it does not support changing
the associated IP address.

```bash
# az network nic create -g <resource-group> -n <name> -l <region> --vnet-name <vnet-name> --subnet <subnet-name> --public-ip-address <public-ip-name>

$ az network nic create -g intro-rg -n intro-nic -l westus --vnet-name intro-vnet --subnet intro-subnet --public-ip-address intro-pip
```

Note that while at present each NIC can have only one IP address assigned to
it, some types of VMs can have multiple NICs.  However, in order to enable this feature, the VM must be assigned at least two NICs at creation.  A VM that is
created with a single NIC will not support adding a second one later, even if
its type allows this in general.

You can set various properties of a NIC, including its [Network Security
Group](nsgs.md), its [DNS](dns.md) name, and IP forwarding, with

```bash
# ?
``` 

Other properties of the NIC are stored within an IP configuration.  A NIC can
have more than one IP configuration.  These are not top-level resources; they
exist only within their parent NIC.  The IP configuration includes not only
the information shown above, but also what [load balancer](lbs.md) pool the
NIC is part of.  An IP configuration is created for you if you don't create
one yourself.  You can set properties on the IP configuration with

```bash
# ?
```

Let's create two more NICs, with DIPs rather than PIPs.  We'll use these
later.  Note that these are in the other subnet.

```bash
$ az network nic create -g intro-rg -n intro-nic-be1 -l westus --vnet-name intro-vnet --subnet intro-subnet2
```

```bash
$ az network nic create -g intro-rg -n intro-nic-be2 -l westus --vnet-name intro-vnet --subnet intro-subnet2
```

```bash
$ az network nic list -g intro-rg
```
