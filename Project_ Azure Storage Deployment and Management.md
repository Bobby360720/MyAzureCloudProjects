# Project: Azure Storage Deployment and Management

#### **1\. Project Overview**

The goal of this project is to guide the deployment, configuration, and management of Azure Storage using both the Azure CLI and the Azure Portal. The project will cover:

*   Setup prerequisites.
*   Best practices.
*   Key concepts.
*   Benefits.
*   A complete architecture to test the setup.

#### **2\. Setup Prerequisites**

1. **Azure Subscription**: Ensure you have an active Azure subscription with permissions to create and manage storage accounts.
2. **Azure CLI**: Install the [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) and ensure you are logged in (`az login`).
3. **Azure Portal Access**: Access the [Azure Portal](https://portal.azure.com).
4. **Microsoft Entra ID** : To manage identity and access for secure connections.
5. **Azure Storage Explorer** (optional): To interact with storage accounts in a visual environment.

#### **3\. Best Practices**

*   **Use Managed Identities** for secure, automated interactions between services.
*   **Enable Data Encryption**: Implement encryption at rest and optionally client-side encryption.
*   **Choose Appropriate Redundancy Options**: Use Geo-redundant storage (GRS) or Locally redundant storage (LRS) based on your data durability needs.
*   **Apply Network Security Controls**: Limit access to storage accounts using Network Security Groups (NSGs) and private endpoints.
*   **Monitor and Manage Access**: Implement Azure RBAC for granular access control.

#### **4\. Key Concepts**

*   **Blob Storage**: Object storage for unstructured data.
*   **Azure Files**: Managed file shares with SMB or NFS support.
*   **Queue Storage**: Reliable message queuing.
*   **Table Storage**: NoSQL store for structured data.
*   **Redundancy**: Options like LRS, ZRS (Zone-redundant storage), and GRS.
*   **Encryption**: Client-side and server-side encryption for data protection.
*   **Networking**: Using Private Link for enhanced security.

#### **5\. Benefits**

*   **High Availability and Durability**: Ensures data is accessible even during hardware failures or regional disasters.
*   **Security**: Built-in encryption and identity-based access.
*   **Scalability**: Capable of handling petabytes of data.
*   **Accessibility**: Data can be accessed globally over HTTP/HTTPS with SDKs and APIs.
*   **Managed Services**: Reduces operational burden by automating hardware maintenance.

#### **6\. Implementation Steps**

**Step-by-Step Guide Using Azure CLI and Azure Portal**

##### **6.1. Create an Azure Storage Account and private endpoint**

*   **Using Azure CLI**:

```plain
az group create --name MyResourceGroup --location eastus

az storage account create --name myteststorageaccount74 --resource-group MyResourceGroup --location eastus --sku Standard_LRS --kind StorageV2

az network private-endpoint create --resource-group MyResourceGroup --name MyPrivateEndpoint --vnet-name MyVNet --subnet MySubnet --private-connection-resource-id $(az storage account show --name myteststorageaccount74 --resource-group MyResourceGroup --query "id" -o tsv) --group-id blob --connection-name mystorageendpointconnection



```

![](https://t9014131694.p.clickup-attachments.com/t9014131694/a8505864-ba32-4c3a-8e37-643a784a9da2/image.png)

##### **6.2. Configure Network Access**

*   **Using Azure CLI**:
*   Create a Virtual Network and Subnet

```plain
# Create a resource group if not already created
az group create --name MyResourceGroup --location eastus

# Create a Virtual Network
az network vnet create --resource-group MyResourceGroup --name MyVNet --address-prefix 10.1.0.0/16 --location eastus

# Create a Subnet
az network vnet subnet create --resource-group MyResourceGroup --vnet-name MyVNet --name MySubnet --address-prefix 10.1.1.0/24

# Adding a Service Endpoint to a Subnet


```

![](https://t9014131694.p.clickup-attachments.com/t9014131694/8ed78401-d12a-43e1-bda2-1ca0cc891cd5/image.png)

![](https://t9014131694.p.clickup-attachments.com/t9014131694/06a33deb-2ea7-4c86-bfcc-602d5ed07138/image.png)

![](https://t9014131694.p.clickup-attachments.com/t9014131694/1dedf06f-d087-4aff-9b39-fbe618724275/image.png)

```plain
az storage account network-rule add --resource-group MyResourceGroup --account-name myteststorageaccount74 --vnet-name MyVNet --subnet MySubnet
 * allow you to apply the network ACLs to your storage account. 
```

##### **6.3. Enable Data Encryption**

*   **Using Azure CLI**:

```plain
az storage account encryption-scope create --resource-group MyResourceGroup --account-name myteststorageaccount74 --name MyEncryptionScope --key-source Microsoft.Storage
```

![](https://t9014131694.p.clickup-attachments.com/t9014131694/4431b96c-0945-4232-8c2f-82bd41c27d16/image.png)

*   **Using Azure Portal**:
    1. Go to **Encryption** under settings.
    2. Choose **Microsoft-managed keys** or configure customer-managed keys using **Azure Key Vault**.

##### **6.4. Create and Configure Blob Storage Container**

*   **Using Azure CLI**:

```plain
az storage container create --name mycontainer --account-name myteststorageaccount74
```

![](https://t9014131694.p.clickup-attachments.com/t9014131694/19e73e07-caf5-4533-a514-d111a7029a81/image.png)

*   **Using Azure Portal**:
    1. Navigate to Storage account > **Containers**.
    2. Click **\+ Container**, name it, set public access level, and create.

##### **6.5. Access Control with Azure AD Integration**

*   **Using Azure CLI**:

```plain
az role assignment create --assignee user@domain.com --role "Storage Blob Data Contributor" --scope /subscriptions/{subscription-id}/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount
```

*   **Using Azure Portal**:
    1. Go to **Access Control (IAM)** in the storage account.
    2. Assign a role like **Storage Blob Data Contributor**.

![](https://t9014131694.p.clickup-attachments.com/t9014131694/d69896e1-2914-4761-9280-a09576bb5421/image.png)

####