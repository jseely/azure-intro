Virtual Machines
================

Azure VMs come in several different [series](https://docs.microsoft.com/en-us/azure/virtual-machines/virtual-machines-linux-sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json),
and a variety of configurations within each.

_A-series_

The A-series actually comprises two distinct classes of VM.  A0-A7 are
value-prices, entry-level, general purpose VMs, offering from 1-8 CPU cores,
.75-56 GiB of RAM, 20-605 GiB of local storage, 1-16 data disks, and 1-4
NICs.

A0-A5 correspond to special keywords in the CLI, they are ExtraSmall, Small,
Medium, Large, and ExtraLarge respectively.

A8-A11, however, are optimized for [compute intensive](https://docs.microsoft.com/en-us/azure/virtual-machines/virtual-machines-linux-a8-a9-a10-a11-specs?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) workloads.  There are
basically two kinds:

* 8 cores, 56 GiB RAM, 2 NICs
* 16 cores, 112 GiB RAM, 4 NICS

Each has 382 GiB local storage, and can take 16 data disks.  This splits into
4 SKUs because A8 and A9 are RDMA-capable; A10 and A11 mirror those but 
without RDMA.

_Av2-series_

A later version of the A-series, with more performant hardware but otherwise
similar to the A-series A0-A7.  Local disk on the Av2 is SSD.  This series
uses the new size nomenclature based on CPU cores, so are named A1_v2, A2_v2,
A4_v2, A8_v2, etc.

_D/DS-series_

The D-series is for more demanding workloads, with more performant CPUs and
more RAM per core than the A-series.  A DS designation is the same as its
corresponding D SKU, but with access to Premium storage.

D-series VMs are available in 1-16 core SKUs, with 3.5-112 GiB RAM, 50-800
GiB of local SSD storage, 2-32 data disks, and 1-8 NICs.  There are two
lines, D1-4 and D11-14.  The latter mirrors the former, but with 4 times the
RAM.

_Dv2/DSv2-series_

A later version of the D-series, with more performant CPUs, and an extra SKU
(D5_v2 & D15_v2) offering even more of everything.

_F/Fs-series_

This series uses the same higher-performing CPU as the Dv2-series, but
at RAM ratios more similar to the A-series.  This is the best value for
pure CPU performance.  They offer 1-16 cores and 2-32 GiB of RAM.

_G/GS-series_

The G-series has the highest RAM ratios.  2-32 cores, with 28-448 GiB
of RAM.

_H-series_

This series is designed to take over from the A8-A11 as the next generation
of high-performance computing VMs.

_N-series_

The N-series is GPU-enabled, with two lines based on the NVIDA M60 and K80.

**Note** Because hardware varies from region to region, not all series/sizes
are available everywhere.  To see the list of VM sizes that are nominally
available in a given region, use

```bash
# az vm list-sizes -l westus
```

Also, be aware that even if a size is nominally available, capacity may be
limited and fully subscribed, making it possible that an attempt to provision
a VM of certain sizes may fail.  Coordinate with the Azure Capacity team in
advance if you anticipate provisioning large numbers of cores, would like
to use sizes not publically available in your target region, or your desired
VM size is consistently failing to provision due to oversubscription.

## CLI

Our first VM is the "jumpbox", a utility VM that allows us to service the
rest of the environment.  It doesn't need to be particularly performant.

```bash
$ az vm create -g intro-rg -n intro-vm --nics intro-nic -l westus --image UbuntuLTS --ssh-key-value ~/.ssh/id_rsa.pub --size Standard_A1
info:    Executing command vm create
```

Now we'll create the two VMs that will be behind the load balancer.  Note that
we're setting the boot diagnostics to be stored in the original strorage
account.

```bash
$ az vm create -g intro-rg -n intro-vm-be1 --nics intro-nic-be1 -l westus --image UbuntuLTS --ssh-key-value ~/.ssh/id_rsa.pub --size Standard_DS1 --availability-set intro-availset
```

```bash
$ az vm create -g intro-rg -n intro-vm-be2 --nics intro-nic-be2 -l westus --image UbuntuLTS --ssh-key-value ~/.ssh/id_rsa.pub --size Standard_DS1 --availability-set intro-availset
```
