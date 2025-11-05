# TP2 : Cloud Features & Shellscripts


## I. Un p'tit nom DNS

### 🌞 Prouvez que c'est effectif

>  az network public-ip show --resource-group GambergeLand --name azure1.tp1PublicIP

``` 
{
  "etag": "W/\"edddbe3c-965a-4ae8-bf92-1d48310dc0e1\"",
  "id": "/subscriptions/f1f8efce-4dfd-40de-8590-a78f3fdb12ca/resourceGroups/GambergeLand/providers/Microsoft.Network/publicIPAddresses/azure1.tp1PublicIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "40.89.160.61",
  "ipConfiguration": {
    "id": "/subscriptions/f1f8efce-4dfd-40de-8590-a78f3fdb12ca/resourceGroups/GambergeLand/providers/Microsoft.Network/networkInterfaces/azure1.tp1VMNic/ipConfigurations/ipconfigazure1.tp1",
    "resourceGroup": "GambergeLand"
  },
  "ipTags": [],
  "location": "francecentral",
  "name": "azure1.tp1PublicIP",
  "provisioningState": "Succeeded",
  "publicIPAddressVersion": "IPv4",
  "publicIPAllocationMethod": "Static",
  "resourceGroup": "GambergeLand",
  "resourceGuid": "0ce36004-48fd-4eb7-8f0a-65cbe765d239",
  "sku": {
    "name": "Standard",
    "tier": "Regional"
  },
  "tags": {},
  "type": "Microsoft.Network/publicIPAddresses"
}
```

### 


> PS C:\Users\exoli> az network public-ip update --resource-group GambergeLand --name azure1.tp1PublicIP --dns-name meow-tp2-ne44
```

{
  "ddosSettings": {
    "protectionMode": "VirtualNetworkInherited"
  },
  "dnsSettings": {
    "domainNameLabel": "meow-tp2-ne44",
    "fqdn": "meow-tp2-ne44.francecentral.cloudapp.azure.com"
  },
  "etag": "W/\"a8f7451d-1eb8-4b8c-bfcb-f1912a4bda0a\"",
  "id": "/subscriptions/f1f8efce-4dfd-40de-8590-a78f3fdb12ca/resourceGroups/GambergeLand/providers/Microsoft.Network/publicIPAddresses/azure1.tp1PublicIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "40.89.160.61",
  "ipConfiguration": {
    "id": "/subscriptions/f1f8efce-4dfd-40de-8590-a78f3fdb12ca/resourceGroups/GambergeLand/providers/Microsoft.Network/networkInterfaces/azure1.tp1VMNic/ipConfigurations/ipconfigazure1.tp1",
    "resourceGroup": "GambergeLand"
  },
  "ipTags": [],
  "location": "francecentral",
  "name": "azure1.tp1PublicIP",
  "provisioningState": "Succeeded",
  "publicIPAddressVersion": "IPv4",
  "publicIPAllocationMethod": "Static",
  "resourceGroup": "GambergeLand",
  "resourceGuid": "0ce36004-48fd-4eb7-8f0a-65cbe765d239",
  "sku": {
    "name": "Standard",
    "tier": "Regional"
  },
  "tags": {},
  "type": "Microsoft.Network/publicIPAddresses"
}
```
> PS C:\Users\exoli> curl -I http://meow-tp2-ne44.francecentral.cloudapp.azure.com:8000

```
{
  "ddosSettings": {
    "protectionMode": "VirtualNetworkInherited"
  },
  "dnsSettings": {
    "domainNameLabel": "meow-tp2-ne44",
    "fqdn": "meow-tp2-ne44.francecentral.cloudapp.azure.com"
  },
  "etag": "W/\"a8f7451d-1eb8-4b8c-bfcb-f1912a4bda0a\"",
  "id": "/subscriptions/f1f8efce-4dfd-40de-8590-a78f3fdb12ca/resourceGroups/GambergeLand/providers/Microsoft.Network/publicIPAddresses/azure1.tp1PublicIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "40.89.160.61",
  "ipConfiguration": {


```
## I. cloud-init

### 🌞 Tester cloud-init

> az vm create --resource-group GambergeLand --name azure3.tp2 --image Ubuntu2204 --size Standard_B1s --location francecentral --custom-data ./cloud-init.txt --public-ip-address "" --vnet-name vnet-francecentral --subnet snet-francecentral-1 --generate-ssh-keys


> sudo systemctl status cloud-init

> cloud-init status
> python3 -c 'import crypt; print(crypt.crypt("motdepasse", crypt.mksalt(crypt.METHOD_SHA512)))'
