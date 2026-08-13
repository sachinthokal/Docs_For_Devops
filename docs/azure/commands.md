# Azure CLI (`az`) Commands & Bicep Reference ⚡

Categorized cheat sheet for managing Azure Resource Groups, Virtual Machines, Networking, Storage, and Bicep code templates.

---

## 1. Resource Groups & Management

```bash
# Create a Resource Group
az group create --name rg-production-eastus --location eastus

# List all Resource Groups in subscription
az group list --output table

# Delete a Resource Group and all resources inside it
az group delete --name rg-production-eastus --yes --no-wait
```

---

## 2. Virtual Machines (Compute)

```bash
# Create an Ubuntu Linux VM with SSH keys
az vm create   --resource-group rg-production-eastus   --name vm-web-01   --image Ubuntu2204   --admin-username azureuser   --generate-ssh-keys

# Open Port 80 for web traffic
az vm open-port --port 80 --resource-group rg-production-eastus --name vm-web-01

# List running VMs with IP addresses
az vm list-ip-addresses --output table
```

---

## 3. Storage Accounts & Virtual Networks (VNet)

```bash
# Create Virtual Network and Subnet
az network vnet create   --resource-group rg-production-eastus   --name vnet-prod   --address-prefix 10.0.0.0/16   --subnet-name subnet-app   --subnet-prefix 10.0.1.0/24

# Create Blob Storage Account
az storage account create   --name stproddata2026   --resource-group rg-production-eastus   --location eastus   --sku Standard_LRS
```

---

## 4. Sample Bicep Template (`main.bicep`)

```bicep
param location string = 'eastus'
param storageAccountName string = 'stappdata${uniqueString(resourceGroup().id)}'

resource storageAccount 'Microsoft.Storage/storageAccounts@2023-01-01' = {
  name: storageAccountName
  location: location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'StorageV2'
  properties: {
    accessTier: 'Hot'
  }
}

output storageAccountId string = storageAccount.id
```