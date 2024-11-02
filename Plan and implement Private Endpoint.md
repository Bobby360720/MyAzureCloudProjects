# Plan and implement Private Endpoints

* * *


### **Objective:**

To establish secure connectivity between Azure virtual networks and Azure services using **Private Endpoints**.

* * *

### **1\. Understand Private Endpoints**

#### **Objective:**

Gain a foundational understanding of **Private Endpoints**, how they function, and their benefits.

#### **Key Concepts**:

*   A **Private Endpoint** is a network interface that assigns a private IP address from the VNet, allowing secure access to Azure services over a private connection.
*   Used to connect to services like **Azure Storage**, **Azure SQL Database**, **Azure Cosmos DB**, etc.
*   **Azure Private Link** enables connectivity from your VNet to the service.

#### **Key Properties**:

*   **Name**: Unique identifier within the resource group.
*   **Subnet**: The subnet in the VNet to which the private endpoint is connected.
*   **Private-Link Resource**: The resource connected using a private endpoint.
*   **Connection Status**: States like Approved, Pending, Rejected, etc.

* * *

### **2\. Set Up Prerequisites**

#### **Objective**:

Ensure that your Azure environment meets the prerequisites for implementing private endpoints.

#### **Implementation Steps**:

1. **Azure Subscription**:
    *   Ensure you have an active Azure subscription with appropriate permissions.
2. **Virtual Network Setup**:
    *   Create a VNet with at least one subnet for deploying the private endpoint.

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

![](https://t9014131694.p.clickup-attachments.com/t9014131694/f4632d5d-516d-4a18-acd6-031d35199f9f/image.png)

1. **Register Resource Provider**:
    *   Register the Microsoft network resource provider for your subscription.

#### **CLI Example: Register Resource Provider**:

```plain
# Register the Microsoft network resource provider
az provider register --namespace Microsoft.Network
```

![](https://t9014131694.p.clickup-attachments.com/t9014131694/04413217-46dd-4093-8132-40f45372c87a/image.png)

* * *

### **3\. Create a Private Endpoint**

#### **Objective**:

Deploy a private endpoint to establish a private connection between the VNet and the Azure service.

#### **Implementation Steps**:

1. **Create the Private Endpoint**:
    *   Specify the VNet, subnet, and Azure service to connect using a private endpoint.

#### **CLI Example: Create a Private Endpoint for Azure Storage**:

```plain
# Create a Storage Account 
 az storage account create \
    --name <storage-account-name> \
    --resource-group <resource-group-name> \
    --location <location> \
    --sku <sku-name> \
    --kind <kind>

# Create a private endpoint for Azure Storage
az network private-endpoint create \
  --resource-group <resource-group-name> \
  --name MyPrivateEndpoint \
  --vnet-name MyVNet \
  --subnet MySubnet \
  --private-connection-resource-id /subscriptions/<sub-id>/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/MyStorageAccount3600 \
  --group-ids blob \
  --connection-name MyPrivateConnection
```

![](https://t9014131694.p.clickup-attachments.com/t9014131694/20dc9a26-4d5b-4791-9180-f9917be8eb50/image.png)

1. **Configure Network Security**:
    *   Ensure that the subnet’s NSG allows the required traffic to the private endpoint.

#### **CLI Example: NSG Rule for Private Endpoint**:

```plain
# Create an NSG
az network nsg create \
    --name <nsg-name> \
    --resource-group <resource-group-name> \
    --location <location>

# Allow traffic to the private endpoint
az network nsg rule create \
  --resource-group MyResourceGroup \
  --nsg-name MyNSG \
  --name AllowPrivateEndpoint \
  --priority 100 \
  --direction Inbound \
  --access Allow \
  --protocol Tcp \
  --destination-port-ranges 443 \
  --source-address-prefixes '*' \
  --destination-address-prefixes PrivateEndpointIP
# Assoicate an NSG to a subnet
az network subnet update \
    --name <subnet-name> \
    --resource-group <resource-group-name> \
    --vnet-name <vnet-name> \
    --network-security-group <nsg-name>
```

![image.png](https://ata-portal-media-master.s3.amazonaws.com/incident_uploads/md5/27e/7f8/b24518ace623e85b0652c25f35/0536bd11-42c6-4caa-ab4e-2c8103687c3a.png?AWSAccessKeyId=ASIA35EH3Z5YMG4T46J2&Signature=o%2Bt0bW2Jps646CQnWaIs5wrsS8I%3D&x-amz-security-token=IQoJb3JpZ2luX2VjEEUaCXVzLXdlc3QtMiJHMEUCIQClMRIM1WpDre6NWGiMWpPuTTKAQSuiuHlVJ7IJlLDe%2BwIgNz8HLtafH%2BzZIQ60JxCqBzLgWsyVzguTjuDoUZRJddoq%2FAMIvv%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FARAFGgw4MTg0NzYyMDc5ODQiDMDIMP9PHGgEq6%2Fv3yrQAzh7bJPBKhw%2BMn7z7FSOVrY76CLC4tXPwWAw3K8rW2kJvtIN0LKoENV35zGYuEuG9iWF%2BxiFkNS59L0PXKx22CKPVDsLtRnTVv%2BGRHLCnFhkA8Oy7r%2FbM6h8CR4q%2FH7CDTzIlb5lSPxWcZDiOTO4ylMB7id9Zl9pLlLY6rhvQbiD%2BAwOc62DyqaJY%2FXaheu%2BNB6v4DF2pX0kmk7KK2xw8SQp0ZzhkjF4UUlZ73LAencAr8lE55TWlc78ZFRsfKZ2NGhzQTz8ulJyGpZMpGAKjR41ytCIF3nsjyUQGCWIY%2BiKMWgDSjMdncAJEgmdXaBDtJVutaOqC18yA2q6%2BfcI4Jgim4JZgKOA5ZOvnuQFhP7JFhcq3Z2uhMKs%2FdobDDHtdxUTzbeZByyEZE2wV0eKlO8Nbs7GEhv4w671xZdPf256UtT%2FKZTx%2Bu%2BZhr8Vw3k2XqovPKV9Lm22f0DzdFvHo27kC2fnGaGUQS6A1sF2B8YTbSM1mQMaG%2Ff8fmJ3m5vlcxV7d83WCaOyfX8Tp6B85F6jK0oGI6Uf6v0222albTdGeQwI9UV4mp6opNiHfZdvRV64Ifw3u6L%2BDUZviCFYKAxI%2B6EdKu19078XEk2uwDQDMOjLmLkGOqUBK%2B0q1cI%2FCiDUZX0pXR3klGF69l%2FddFdXdx%2FlzKbhuEi60nbg%2F2EckRt%2B5qmp33EP435QqgNABV3uyvgJbhm9WrRsIMYTCVpjAUKdOnnsVXjW6e%2BFREZibMG3FbMUvTtVcUzIycHI5bzc%2FHmCiyUcdAPEGYqcVshb9add8e19WSHcsRRHPKjZ48UfK7D8d%2BZNujb0sM9uB5CttnZIE3rp4ZPTqArq&Expires=1730576837)

![](https://t9014131694.p.clickup-attachments.com/t9014131694/15f727d0-0518-49e9-8f71-38691cbc891a/image.png)

1. **Configure DNS Settings**:
    *   Set up DNS to resolve the private endpoint's IP address for the Azure service. This often requires a **Private DNS Zone**.

#### **CLI Example: Create Private DNS Zone**:

```plain
# Create a private DNS zone
az network private-dns zone create \
  --resource-group MyResourceGroup \
  --name privatelink.blob.core.windows.net

# Link DNS zone to VNet
az network private-dns link vnet create \
  --resource-group MyResourceGroup \
  --zone-name privatelink.blob.core.windows.net \
  --name MyDNSLink \
  --virtual-network MyVNet \
  --registration-enabled false

# Add DNS record for private endpoint
az network private-dns record-set a add-record \
  --resource-group MyResourceGroup \
  --zone-name privatelink.blob.core.windows.net \
  --record-set-name MyStorageAccount \
  --ipv4-address PrivateEndpointIP
```

![](https://t9014131694.p.clickup-attachments.com/t9014131694/b61fe3d9-83f4-4efe-9306-a8b6d828aa73/image.png)

![](https://t9014131694.p.clickup-attachments.com/t9014131694/9db6abc6-cde2-4060-93fb-ae2d64c87877/image.png)

![](https://t9014131694.p.clickup-attachments.com/t9014131694/005ecd8a-e6ba-4c2d-be67-4a335a7103c1/image.png)

* * *

### **4\. Approve or Reject Connection Requests**

#### **Objective**:

Manage the connection request approval for the private endpoint.

#### **Implementation Steps**:

1. **Automatic Approval**:
    *   If you own or have permissions for the private-link resource, the connection can be approved automatically.
2. **Manual Approval**:
    *   If you don’t have permissions, a connection request is initiated and needs to be manually approved.

#### **CLI Example: Approve Connection Request**:

```plain
# Get Resource ID
az network private-endpoint-connection list --resource-group MyResourceGroup --name MyPrivateConnection
# Approve a pending private endpoint connection
az network private-endpoint-connection approve --id </subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/privateEndpointConnections/{privateEndpointConnectionName}
 --description "Approved connection"
```

#### **CLI Example: Reject Connection Request**:

```plain
# Reject a private endpoint connection
az network private-endpoint-connection reject \
  --resource-group MyResourceGroup \
  --name MyPrivateConnection \
  --description "Rejected connection"
```

* * *

### **5\. Implement Best Practices and Security Considerations**

#### **Objective**:

Ensure that private endpoints are implemented securely and effectively.

#### **Best Practices**:

*   **Use NSGs and UDRs**: Apply appropriate network policies to control traffic.
*   **Configure DNS Correctly**: Use Private DNS Zones to resolve private IPs.
*   **Review Connection Details Regularly**: Monitor connections, approvals, and security settings.

#### **Key Security Considerations**:

*   **Network Security**: NSGs and UDRs should be configured to control traffic effectively.
*   **Data Exfiltration Protection**: Use NSGs to prevent unintended data exfiltration.
*   **DNS Configuration**: Ensure DNS resolution is correctly set to private IPs.

* * *

### **6\. Test and Validate the Configuration**

#### **Objective**:

Ensure that the private endpoint is working as expected and providing secure connectivity.

#### **Testing Steps**:

1. **Validate Connectivity**:
    *   Ensure the VNet can connect to the Azure service through the private endpoint.

#### **CLI Example: Test Connectivity**:

```plain
# Test connectivity to the private endpoint
az network private-endpoint list-types --location eastus
```

![](https://t9014131694.p.clickup-attachments.com/t9014131694/38b2df7e-daff-4795-a524-17d2f85ec523/image.png)

1. **Check Effective Routes**:
    *   Confirm that the route table includes a route for the private endpoint.

#### **CLI Example: View Effective Routes**:

```sql
# Check Routes for a Private Endpoint
az network private-endpoint show \
  --name <PrivateEndpointName> \
  --resource-group MyResourceGroup \
  --output table
```

![](https://t9014131694.p.clickup-attachments.com/t9014131694/b619416c-f8b7-407e-8a91-369f66837da8/image.png)

1. **Verify DNS Resolution**:
    *   Ensure DNS queries resolve to the private IP of the private endpoint.

#### **CLI Example: Verify DNS Resolution**:

```plain
# Deploy a VM in the Same VNet as the Private Endpoint
az vm create `
  --resource-group MyResourceGroup `
  --name MyTestVM `
  --image Win2019Datacenter `
  --vnet-name MyWindowsVM `
  --subnet MySubnet `
  --admin-username azureuser `
  --admin-password MyP@ssw0rd123


# Enable Network Watcher (If Not Already Enabled)
az network watcher configure `
  --locations eastus `
  --resource-group MyResourceGroup `
  --enabled true
# Obtain the Private IP of the Private Endpoint
az network private-endpoint show `
  --name MyPrivateEndpoint `
  --resource-group MyResourceGroup `
  --query 'customDnsConfigs[0].ipAddresses[0]' `
  --output tsv

#Connect to the VM using Remote Desktop:

Use Remote Desktop Connection (RDP) to connect to the Windows VM:
Open the Remote Desktop Connection app.
Enter the VM’s public IP address.
Log in using the credentials specified during VM creation (e.g., azureuser and MyP@ssw0rd123).
# Verify DNS resolution to private IP
nslookup <ServiceName>.<PrivateLinkDomain>
nslookup mystorageaccount3600.blob.core.windows.net


```

![](https://t9014131694.p.clickup-attachments.com/t9014131694/3b4ba2a3-9cc7-41fa-a096-b4814d519c56/image.png)

![](https://t9014131694.p.clickup-attachments.com/t9014131694/25dc3134-bd08-4be6-9055-2dcd75c2909b/image.png)

![](https://t9014131694.p.clickup-attachments.com/t9014131694/61084b6a-0a8a-4b9a-8808-87240f9ebf32/image.png)

* * *

### **7\. Monitor and Troubleshoot Private Endpoint Configuration**

#### **Objective**:

Use Azure monitoring tools to track and troubleshoot issues related to private endpoints.

#### **Implementation Steps**:

1. **Enable Diagnostics**:
    *   Enable diagnostics on the Azure service and monitor the private endpoint connection.

#### **CLI Example: Enable Diagnostics on Azure Storage**:

```plain
# Enable diagnostics on Azure Storage
az storage logging update `
  --account-name MyStorageAccount3600 `
  --resource-group MyResourceGroup `
  --services b `
  --log rwd `
  --retention 7


```

1. **Use Network Watcher**:
    *   Use **Azure Network Watcher** to verify network connectivity, diagnose issues, and monitor traffic flow.

* * *

### **Conclusion**:

This project outlines the complete implementation of **Azure Private Endpoints** to secure communication between VNets and Azure services. You have learned how to create private endpoints, configure DNS settings, manage connection approvals, and monitor connectivity to ensure private and secure access.
