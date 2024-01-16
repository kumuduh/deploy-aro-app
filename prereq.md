# Prerequisites for deploying an enterprise application

## Environment Variables
```bash
RGNAME='kevin-aro-rg'
LOCATION='eastus'
AROCLUSTER='poc-kevin-cluster'
PULLSECRETLOCATION=@/Users/kevincollins/Downloads/pull-secret.json
```

# VNet Name and IP Addresses
```bash
VNET_NAME='aro-kevin-vnet'
MASTERAROSUBNET_NAME=master-aro-subnet
WORKERAROSUBNET_NAME=worker-aro-subnet
VNET_PREFIX='10.1.0.0/16'
MASTERAROSUBNET_PREFIX='10.1.0.0/23'
WORKERAROSUBNET_PREFIX='10.1.2.0/23'
PRIVATEENDPOINTSUBNET_PREFIX='10.1.6.0/25'
PRIVATEENDPOINTSUBNET_NAME='PrivateEndpoint-subnet'
```

# Create the resource group
```bash
az group create --name $RGNAME --location $LOCATION
```

## Create a VNET with three subnets
```bash
# Create  virtual network
az network vnet create \
   --resource-group $RGNAME \
   --name $VNET_NAME \
   --address-prefixes $VNET_PREFIX

# master-aro-subnet
az network vnet subnet create \
  --resource-group $RGNAME \
  --vnet-name $VNET_NAME \
  --name $MASTERAROSUBNET_NAME \
  --address-prefixes $MASTERAROSUBNET_PREFIX \
  --disable-private-link-service-network-policies true

# worker-aro-subnet
az network vnet subnet create \
  --resource-group $RGNAME \
  --vnet-name $VNET_NAME \
  --name $WORKERAROSUBNET_NAME \
  --address-prefixes $WORKERAROSUBNET_PREFIX \
  --disable-private-link-service-network-policies true

# PrivateEndpoint-subnet
az network vnet subnet create \
  --resource-group $RGNAME \
  --vnet-name $VNET_NAME \
  --name $PRIVATEENDPOINTSUBNET_NAME \
  --address-prefixes $PRIVATEENDPOINTSUBNET_PREFIX \
  --disable-private-endpoint-network-policies true
```

## Create an ARO Cluster
```bash
az aro create \
  --resource-group $RGNAME \
  --name $AROCLUSTER \
  --vnet $VNET_NAME \
  --master-subnet $MASTERAROSUBNET_NAME \
  --worker-subnet $WORKERAROSUBNET_NAME \
  --apiserver-visibility Private \
  --ingress-visibility Private \
  --pull-secret $PULLSECRETLOCATION
```
