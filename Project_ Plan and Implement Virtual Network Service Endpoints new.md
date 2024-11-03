# Project: Plan and Implement Virtual Network Service Endpoints

### **Objective:**

To implement Azure Virtual Network (VNet) Service Endpoints to provide secure, direct connectivity from virtual networks to Azure services over an optimized route on the Azure backbone network without requiring public IPs.

* * *

### **1\. Understand Virtual Network Service Endpoints**

#### **Objective:**

Gain a foundational understanding of Azure **VNet Service Endpoints**, how they function, and their benefits.

#### **Key Concepts**:

*   **Service Endpoint Overview**:
    *   Provides secure and direct connectivity from VNet to Azure services.
    *   Extends VNet identity to Azure services, improving security by allowing only VNet traffic.
    *   Keeps traffic on the Azure backbone network.
    *   Enables simplified management without the need for public IP addresses.

#### **Supported Azure Services**:

*   **Azure Storage**, **Azure SQL Database**, **Azure Synapse Analytics**, **Azure Key Vault**, **Azure Cosmos DB**, and more

#### **Key Benefits**:

*   **Improved Security**: Secure Azure service resources to specific VNets.
*   **Optimal Routing**: Traffic is routed directly over the Azure backbone network.
*   **Simplified Management**: No public IPs or NAT configurations are needed.

#### **Limitations**:

*   Available only for VNets created via the **Azure Resource Manager** deployment model.
*   It only works for traffic originating within VNets, not for on-premises connections.
*   Service endpoints must be in the same region as the VNet for specific services.

* * *

### **2\. Set Up Prerequisites**

#### **Objective**:

Ensure that your Azure environment meets the prerequisites for implementing VNet service endpoints.

#### **Implementation Steps**:

1. **Azure Subscription**:
    *   Ensure that you have an active Azure subscription.
2. **Virtual Network Setup**:
    *   Create a VNet with at least one **subnet** where the service endpoint will be enabled.

#### **CLI Example: Create a Virtual Network**:

```sql
# Create a resource group
az group create --name MyResourceGroup --location eastus

# Create a virtual network with a subnet
az network vnet create \
  --resource-group MyResourceGroup \
  --name MyVNet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name MySubnet \
  --subnet-prefix 10.0.1.0/24
```

![](https://t9014131694.p.clickup-attachments.com/t9014131694/56584d95-9b6d-49fe-b855-c1e9517ab797/image.png)

![](https://t9014131694.p.clickup-attachments.com/t9014131694/8787799b-2629-41aa-bec0-b40994502635/image.png)

* * *

### **3\. Enable Service Endpoints on the Subnet**

#### **Objective**:

Configure service endpoints for specific Azure services on the designated subnet of the VNet.

#### **Implementation Steps**:

1. **Enable Service Endpoints**:
    *   Choose the Azure service (e.g., Azure Storage, Azure SQL) and enable service endpoints on the subnet.

#### **CLI Example: Enable Service Endpoint**:

```plain
# Enable service endpoint for Azure Storage on a subnet
az network vnet subnet update \
  --resource-group MyResourceGroup \
  --vnet-name MyVNet \
  --name MySubnet \
  --service-endpoints Microsoft.Storage
```

![](https://t9014131694.p.clickup-attachments.com/t9014131694/f6655654-15c6-4450-9c38-2db5e5542048/image.png)

1. **Verify Service Endpoint Configuration**:
    *   Confirm that the service endpoint is enabled on the subnet.

#### **CLI Example: Verify Service Endpoint**:

```plain
# Get details of the subnet to verify the service endpoint configuration
az network vnet subnet show \
  --resource-group MyResourceGroup \
  --vnet-name MyVNet \
  --name MySubnet
```

* * *

### **4\. Configure Azure Services to Use the Service Endpoint**

#### **Objective**:

Secure the Azure service resource to the virtual network using the enabled service endpoint.

#### **Implementation Steps**:

1. **Add a Virtual Network Rule**:
    *   Add a VNet rule to allow access to the Azure service resource from the specified VNet.

#### **CLI Example: Add VNet Rule to Azure SQL**:

```plain
# Create a new subnet within the existing VNet
az network vnet subnet create \
  --resource-group MyResourceGroup \
  --vnet-name MyVNet \
  --name MySQLSubnet \
  --address-prefix 10.0.2.0/24

# Enable service endpoint for Azure SQL on the subnet
az network vnet subnet update \
  --resource-group MyResourceGroup \
  --vnet-name MyVNet \
  --name MySQLSubnet \
  --service-endpoints Microsoft.Sql

# Create a SQL server
az sql server create \
  --name MySqlServer \
  --resource-group MyResourceGroup \
  --location eastus \
  --admin-user myadmin \
  --admin-password MyP@ssw0rd123

# Create a SQL database
az sql db create \
  --resource-group MyResourceGroup \
  --server MyTestSqlServer360 \
  --name MyTestDatabase360 \
  --service-objective basic

# Add a virtual network rule to secure Azure SQL to the VNet
az sql server vnet-rule create \
  --name MyVNetRule \
  --resource-group MyResourceGroup \
  --server MyTestSqlServer360 \
  --subnet /subscriptions/<sub-id>/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet/subnets/MySubnet
```

![](https://t9014131694.p.clickup-attachments.com/t9014131694/3ca6f7bd-5ec7-44b0-9aec-dc9e7f56a620/image.png)

![](https://t9014131694.p.clickup-attachments.com/t9014131694/da368bb1-8313-4652-a7a3-80516e05255c/image.png)

![](https://t9014131694.p.clickup-attachments.com/t9014131694/a867e8aa-05b2-4d1e-98f0-5cce009d6ae0/image.png)

1. **Validate VNet Service Endpoint Access**:
    *   Confirm that access to the service is restricted to the specified VNet.

#### **CLI Example: Validate Access to Azure SQL**:

```plain
# Check Azure SQL VNet rule
az sql server vnet-rule list \
  --resource-group MyResourceGroup \
  --server MyTestSqlServer360
```

![](https://t9014131694.p.clickup-attachments.com/t9014131694/55f3c5ba-e6cb-4982-816f-691e9061bc6d/image.png)

* * *

### **5\. Implement Best Practices and Security Considerations**

#### **Objective**:

Ensure that VNet service endpoints are implemented securely and effectively.

#### **Best Practices**:

*   **Enable Service Endpoints on Subnets**: Enable endpoints only on subnets that require secure connectivity.
*   **Secure Azure Services**: Use VNet rules to restrict access to Azure services.
*   **Use Network Security Groups (NSGs)**:
    *   Control outbound traffic to Azure services using **service tags** in NSGs.
    *   Use the service tag (e.g., **AzureStorage**, **Sql**) as the destination in NSG rules.

#### **CLI Example: Configure NSG Rule for Service Endpoint Traffic**:

```plain
# Create a NSG
az network nsg  create --name MyNSG --resource-group MyResourceGroup

# Create an NSG rule to allow traffic to Azure Storage
az network nsg rule create \
  --resource-group MyResourceGroup \
  --nsg-name MyNSG \
  --name AllowAzureStorage \
  --priority 100 \
  --direction Outbound \
  --access Allow \
  --destination-address-prefixes Storage \
  --destination-port-ranges 443 \
  --protocol Tcp

# Assoicate an NSG to a VNet
az network vnet subnet update --resource-group MyResourceGroup --vnet-name MyVNet --name MySubnet --network-security-group MyNSG
```

![](https://t9014131694.p.clickup-attachments.com/t9014131694/180b6646-8fda-4df9-85bf-75b97e7aee8a/image.png)

![](https://t9014131694.p.clickup-attachments.com/t9014131694/98dc5afa-c261-427b-a920-acbb3d32251b/image.png)

#### **Key Security Considerations**:

*   **Temporary Interruption**: Enabling service endpoints causes a switch from public to private IP addresses, closing existing TCP connections. Plan the configuration during non-critical times.
*   **Firewall Configurations**: Adjust firewall rules to allow private IP addresses for traffic to the service.

* * *

### **6\. Test and Validate the Configuration**

#### **Objective**:

Ensure that the VNet service endpoints are working as expected and providing secure connectivity.

#### **Testing Steps**:

1. **Verify Service Traffic**:
    *   Check that traffic from the VNet uses the private IP address to access the service.
2. **Validate Network Routes**:
    *   Confirm that the route table includes a specific route for the service endpoint.

#### **CLI Example: Check Effective Routes**:

```sql
# Define variables
$location="eastus"
$resourceGroup="MyResourceGroup"
$vnetName="MyVNet"
$subnetName="MySubnet"
$sqlServerName="MyTestSqlServer360"
$sqlAdminUser="azureuser"
$sqlAdminPassword="MyP@ssw0rd123"


# Create a Resource Group
az group create --name $resourceGroup --location $location

# Create a VNet with a subnet
az network vnet create --resource-group $resourceGroup --name $vnetName  --address-prefix 10.0.0.0/16  --subnet-name $subnetName --subnet-prefix 10.0.1.0/24

# Enable Service Endpoint for Microsoft.SQL on the subnet
az network vnet subnet update --resource-group $resourceGroup --vnet-name $vnetName --name $subnetName --service-endpoints Microsoft.Sql

# Create Azure SQL Server
az sql server create --name $sqlServerName --resource-group $resourceGroup --location $location --admin-user $sqlAdminUser --admin-password $sqlAdminPassword

# Create a SQL Database on the server
az sql db create --resource-group $resourceGroup --server $sqlServerName --name "MyDatabase"  --service-objective S0

# Get the subnet ID
$subnetId = $(az network vnet subnet show --resource-group $resourceGroup --vnet-name $vnetName --name $subnetName --query "id" -o tsv)

# Add a VNet rule to the SQL Server
az sql server vnet-rule create --name "MyVNetRule" --resource-group $resourceGroup --server $sqlServerName --subnet $subnetId


# Create an NSG
$nsgName="MyNSG"
az network nsg create --resource-group $resourceGroup --name $nsgName --location $location

# Add a rule to allow outbound traffic to Azure SQL
az network nsg rule create --resource-group $resourceGroup --nsg-name $nsgName --name "AllowSQL"  --priority 100  --direction Outbound  --access Allow 
--protocol Tcp --destination-port-ranges 1433  --destination-address-prefixes Sql --source-address-prefixes VirtualNetwork


# Associate the NSG with the subnet
az network vnet subnet update `
  --resource-group $resourceGroup `
  --vnet-name $vnetName `
  --name $subnetName `
  --network-security-group $nsgName

  # Verify service endpoint configuration
az network vnet subnet show `
--resource-group $resourceGroup `
--vnet-name $vnetName `
--name $subnetName `
--query "serviceEndpoints"

# Validate SQL Server VNet rule
az sql server vnet-rule list   `
  --resource-group $resourceGroup `
  --server $sqlServerName`

  az vm create --resource-group $resourceGroupName --name MyTestVM  --image Win2022Datacenter --admin-username $sqlAdminUser --admin-password $sqlAdminPassword 



```

![](https://t9014131694.p.clickup-attachments.com/t9014131694/ae877f6b-c3fb-49c1-8768-35f533a0e9d9/image.png)

* * *

### **2\. Install SQL Server Management Studio (SSMS)**

1. **Once logged into the Windows VM**, download and install SSMS from the official website:
    *   Go to: [Download SQL Server Management Studio](https://learn.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms)
    *   Install SSMS by following the on-screen instructions.
2. **Launch SSMS** after installation completes.
    1. ![](https://t9014131694.p.clickup-attachments.com/t9014131694/dfd4a824-5ece-4250-9f58-7183f486dab8/image.png)

* * *

### **3\. Test Connection to Azure SQL Database using SSMS**

1. **Open SSMS** and enter the connection details:
    *   **Server name**: `<sql-server-name>.database.windows.net`
    *   **Authentication**: SQL Server Authentication
    *   **Login**: `<username>`
    *   **Password**: `<password>`
2. **Check Connection Status**:
    *   If the connection is successful, it indicates that traffic is reaching the Azure SQL Database using the service endpoint.
    *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/0d541129-fe6b-49e1-963d-035153099ff9/image.png)

* * *

### **5\. Additional Checks for Service Endpoint Validation**

#### **Using PowerShell to Test Connectivity**

1. **Open PowerShell** on the Windows VM.
2. Use the **Test-NetConnection** command:

```plain
Test-
etConnection -ComputerName <sql-server-name>.database.windows.net -Port 1433
A suc
```

*       *   essful test indicates that the service endpoint is being used to reach the Azure SQL Database on port 1433.

![](https://t9014131694.p.clickup-attachments.com/t9014131694/370eff5d-cba5-47f5-ba7d-597182dcc36c/image.png)

  

* * *

* * *

### **Conclusion**:

This project outlines the complete implementation of Azure **Virtual Network Service Endpoints** to secure communication between VNets and Azure services. You have learned how to enable service endpoints, configure VNet rules, and monitor connectivity to ensure secure, optimized, and direct access to Azure services.