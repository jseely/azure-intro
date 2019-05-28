Load Balancers
==============

An Azure [load balancer](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-overview#load-balancer-features)
 maps incoming network connections from its front end to a back end pool of 
VMs.  Typically the traffic is mapped so as to distribute the load
equally, enabling scale out.  The load balancer monitors the health of
its back-end pool, and will manage the pool accordingly, ceasing to
distribute new connections to VMs that no longer respond suitably to health
probes.

```bash
# az network lb create -g <resource-group> -n <lb-name> -l <location>

$ az network lb create -g intro-rg -n intro-lb -l westus

{
  "loadBalancer": {
    "backendAddressPools": [
      {
        "etag": "W/\"a49bc906-6982-4c6b-a07a-b02a574a2099\"",
        "id": "/subscriptions/f9508c82-cb83-4a04-824a-30a326257ebd/resourceGroups/intro-rg/providers/Microsoft.Network/loadBalancers/intro-lb/backendAddressPools/intro-lbbepool",
        "name": "intro-lbbepool",
        "properties": {
          "provisioningState": "Succeeded"
        },
        "resourceGroup": "intro-rg",
        "type": "Microsoft.Network/loadBalancers/backendAddressPools"
      }
    ],
    "frontendIPConfigurations": [
      {
        "etag": "W/\"a49bc906-6982-4c6b-a07a-b02a574a2099\"",
        "id": "/subscriptions/f9508c82-cb83-4a04-824a-30a326257ebd/resourceGroups/intro-rg/providers/Microsoft.Network/loadBalancers/intro-lb/frontendIPConfigurations/LoadBalancerFrontEnd",
        "name": "LoadBalancerFrontEnd",
        "properties": {
          "privateIPAllocationMethod": "Dynamic",
          "provisioningState": "Succeeded",
          "publicIPAddress": {
            "id": "/subscriptions/f9508c82-cb83-4a04-824a-30a326257ebd/resourceGroups/intro-rg/providers/Microsoft.Network/publicIPAddresses/PublicIPintro-lb",
            "resourceGroup": "intro-rg"
          }
        },
        "resourceGroup": "intro-rg",
        "type": "Microsoft.Network/loadBalancers/frontendIPConfigurations"
      }
    ],
    "inboundNatPools": [],
    "inboundNatRules": [],
    "loadBalancingRules": [],
    "probes": [],
    "provisioningState": "Succeeded",
    "resourceGuid": "e60610ac-bc40-405f-8cdf-845470c04871"
  }
}
```

Load balancers can either be internet-facing, in which case we need a PIP
for its front end, or internal, in which case they are attached to a vnet
and subnet, and acquire a DIP normally.

Here we'll use the second PIP we created earlier to create the load 
balancer's front end.

```bash
$ az network lb frontend-ip create -g intro-rg --lb-name intro-lb -n intro-lb-front --public-ip-address intro-pip-lb
```

Now, we create the backend pool...

```bash
$ az network lb address-pool create -g intro-rg --lb-name intro-lb -n intro-lb-back

{
  "backendIpConfigurations": null,
  "etag": "W/\"fbda07b9-1ea7-40fe-a6f4-6229239cd445\"",
  "id": "/subscriptions/f9508c82-cb83-4a04-824a-30a326257ebd/resourceGroups/intro-rg/providers/Microsoft.Network/loadBalancers/intro-lb/backendAddressPools/intro-lb-back",
  "loadBalancingRules": null,
  "name": "intro-lb-back",
  "outboundRule": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "intro-rg",
  "type": "Microsoft.Network/loadBalancers/backendAddressPools"
}
```

And add the two NICs we created previously to it.

```bash
$ az network nic ip-config create -g intro-rg --nic-name intro-nic-be1 -n default-ip-config --lb-address-pools /subscriptions/f9508c82-cb83-4a04-824a-30a326257ebd/resourceGroups/intro-rg/providers/Microsoft.Network/loadBalancers/intro-lb/backendAddressPools/intro-lb-back
```

```bash
$ az network nic ip-config create -g intro-rg --nic-name intro-nic-be2 -n default-ip-config --lb-address-pools /subscriptions/f9508c82-cb83-4a04-824a-30a326257ebd/resourceGroups/intro-rg/providers/Microsoft.Network/loadBalancers/intro-lb/backendAddressPools/intro-lb-back
```

Now, we tell the load balancer to distribute incoming  HTTP traffic on port 80
across the back end pool.

```bash
$ az network lb rule create -g intro-rg --lb-name intro-lb -n intro-lb-rule-http --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name intro-lb-front --backend-pool-name intro-lb-back
```
 
And we add a [health probe](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-custom-probe-overview) that will ensure that VMs not responding
to HTTP requests are removed from the pool:

```bash
$ az network lb probe create -g intro-rg --lb-name intro-lb -n intro-lb-probe-http --protocol tcp --port 80
```

Finally, let's review the load balancer as we've set it up so far:

```bash
$ az network lb show -g intro-rg -n intro-lb
```
