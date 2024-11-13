# Plan and implement remote access to public endpoints, Azure Bastion and just-in-time (JIT) virtual machine (VM) access

### Project Overview

This project demonstrates how to securely connect to Azure virtual machines (VMs) without exposing RDP/SSH ports to the internet by using **Azure Bastion**. Additionally, it includes setting up **Just-in-Time (JIT) access** to enhance security by allowing inbound traffic to VMs only when needed.

### Prerequisites

1. **Azure Subscription**: Ensure an active Azure subscription.
2. **Azure CLI**: Installed and configured. Download [here](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli).
3. **Virtual Network (VNet)**: Create a Virtual Network (VNet) that includes a subnet named "AzureBastionSubnet" with an address range of /26 or larger, as well as a production subnet.
4. **Virtual Machines**: Existing VMs deployed in the VNet.
5. **Network Security Groups (NSGs)**: Ensure NSGs do not conflict with Bastion or JIT configurations.

### Best Practices

*   **Use Standard SKU for Azure Bastion** for advanced features like custom ports and scaling.
*   **Integrate with Azure Key Vault** for secure management of private keys.
*   **Enable JIT VM Access** to limit exposure and ensure access is time-bound.
*   **Monitor and Audit** connections using Azure Monitor and Log Analytics for enhanced visibility.

### Key Concepts

*   **Azure Bastion**: A managed PaaS service that provides secure RDP/SSH access without the need for public IPs on VMs.
*   **Just-in-Time Access**: Configures VM firewalls to allow inbound traffic only during a pre-approved time window.
*   **VNet Peering**: Allows Azure Bastion to connect to VMs across peered VNets.

### Architecture Overview

The architecture includes:

*   **Azure Bastion** deployed in a VNet.
*   **VMs** in the same or peered VNets.
*   **JIT Access** configured to control inbound traffic.

### Step-by-Step Implementation

#### 1\. Create an Azure Bastion Service Using Azure CLI

**Step 1: Create a Resource Group**

```plain
az group create --name MyTestResourceGroup --location eastus
```

**Step 2: Create a Virtual Network and Subnet**

```plain
az network vnet create --resource-group MyTestResourceGroup --name MyVNet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet --subnet-prefix 10.0.1.0/26

az network vnet subnet create `
    --resource-group MyTestResourceGroup `
    --vnet-name MyVNet `
    --name production `
    --address-prefix 10.0.2.0/24
```

**Step 3: Create a Public IP Address for Bastion**

```plain
az network public-ip create --resource-group MyTestResourceGroup --name MyBastionPublicIP --sku Standard --location eastus
```

Step 4: Create a Network Security Group (NSG) and Associate it with the Subnet

```plain
az network nsg create --resource-group MyTestResourceGroup --name MyNSG

# Add the Required Inbound Rule for Port 3389
az network nsg rule create --resource-group MyTestResourceGroup --nsg-name MyNSG --name AllowRDP --protocol Tcp --priority 1000 --destination-port-range 3389 --access Allow

# Add the Required Outbound Rule for Port 443, 3389

az network nsg rule create --resource-group MyTestResourceGroup --nsg-name MyNSG --name AllowInbound443 --priority 100 --direction Inbound --access Allow --protocol Tcp --destination-port-ranges 443 --source-address-prefixes Internet --destination-address-prefixes *

az network nsg rule create --resource-group MyTestResourceGroup --nsg-name MyNSG --name AllowOutbound443 --priority 110 --direction Outbound --access Allow --protocol Tcp --destination-port-ranges 443 --source-address-prefixes * --destination-address-prefixes Internet

# Assoicate the AzureBAstionSubnet with MyNSG
    az network vnet subnet update --resource-group MyTestResourceGroup --vnet-name MyVNet --name production  --network-security-group MyNSG




```

Step 5: Create a Virtual Machine (VM)

```plain
    az vm create `
  --resource-group MyTestResourceGroup `
  --name MyVM `
  --image Win2022Datacenter `
  --admin-username azureuser `
  --admin-password MyP@ssw0rd123 `
  --vnet-name MyVNet `
  --subnet production `
  --private-ip-address 10.0.2.4
```

**Step 6: Create the Bastion Host**

```plain
az network bastion create --resource-group MyTestResourceGroup --name MyBastionHost --vnet-name MyVNet --public-ip-address MyBastionPublicIP --location eastus --sku Standard
```

#### 2\. Enable Just-in-Time Access Using Azure CLI

**Step 1: Enable JIT Access on a VM**

```plain
az security jit-policy create --resource-group MyResourceGroup --location eastus --vm-ids $(az vm show --resource-group MyResourceGroup --name MyVM --query id -o tsv) --ports '[{"number": 22, "protocol": "TCP", "allowedSourceAddressPrefix": "*", "maxRequestAccessDuration": "PT1H"}]'
```

![](https://t9014131694.p.clickup-attachments.com/t9014131694/28a7c837-645b-4a20-9c7d-09ee67c8d858/image.png)

![](https://t9014131694.p.clickup-attachments.com/t9014131694/3e600f4c-7248-44ce-b68b-80970fb767e8/image.png)

![](https://t9014131694.p.clickup-attachments.com/t9014131694/013e9820-8cb5-426a-b5b5-4b0a596d2ab6/image.png)

![](https://t9014131694.p.clickup-attachments.com/t9014131694/e83a4f65-7ebb-47f8-9243-930554771322/image.png)

### Testing the Project's Functionality

1. **Access the VM via Azure Bastion**:
    *   Go to **Azure Portal** > **Virtual Machines** > Select your VM > **Connect** > **Bastion**.
    *   Verify that the connection opens in your browser without exposing the public IP.
2. **Test JIT Access**:
    *   Request access to the VM via **Defender for Cloud** > **Just-in-Time Access Requests**.
    *   Ensure that access is only available for the configured time period.
    *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/a2552126-560e-4217-9a64-25ba0836859a/image.png)

![](https://t9014131694.p.clickup-attachments.com/t9014131694/58768732-dc61-483e-ab6e-1460bbebb3ef/image.png)

![](https://t9014131694.p.clickup-attachments.com/t9014131694/275aeb5b-febc-4175-bed4-261b67f1bfa3/image.png)

### Benefits

*   **Enhanced Security**: No need for public IPs or exposed ports, reducing attack surfaces.
*   **Centralized Management**: Secure, browser-based access via the Azure portal.
*   **Granular Access Control**: JIT ensures access is time-bound and logged.
*   **TLS Protection**: Encrypted connections using TLS 1.2 and above.

###