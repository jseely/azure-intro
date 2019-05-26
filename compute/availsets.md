Availability Sets
=================

Outside of a VM itself, there are two kinds of events that might cause a VM
to go down: planned events, such as maintenance, and unplanned events, such
as a hardware failure.  In order to ensure that your services remain highly
available despite such events, Azure allows grouping VMs into [availability
sets](https://docs.microsoft.com/en-us/azure/virtual-machines/virtual-machines-linux-manage-availability).

An availabilty set comprises two different kinds of domains: 
update domains and fault domains.  Scheduled maintenance is rolled out
in waves, across update domains: VMs in two different update domains will
not be taken down and rebooted at the same time.  Similarly, fault domains
encapsulate single points of hardware failure for a VM's host: power, network,
etc.  VMs in two different fault domains will not both be affected by the
failure of hardware within a given rack.

When you create an availability set, you specify the number of update
domains (default 5) and fault domains (default 3) that it should span.  New
VMs are added round-robin to these domains.

```bash
# az vm availability-set create -g <resource-group> -n <availability-set-name> -l <region>

$ az vm availability-set create -g intro-rg -n intro-availset -l westus
```

```bash
$ az vm availability-set show -g intro-rg -n intro-availset
```

Note that there is a difference between having a single VM in an availability
set, and not having that VM in one at all.  If the VM is not in an availability
set, then Azure will attempt to notify (e.g. via email) the owner before
maintenance.  If it is in an availability set, then Azure assumes that such
notification is not necessary.

Also: fault domains represent single points of failure in the racks where
the VM host hardware operates, including networking hardware.  VMs require
access to their [VHDs](../storage/vhds.md) as well, though, and these are not mapped to fault
domains.  Make sure VHDs for the VMs in an availablity set are on different
[storage accounts](../storage/accounts.md) for the highest availability.
