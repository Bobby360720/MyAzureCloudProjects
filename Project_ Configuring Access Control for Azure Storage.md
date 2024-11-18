# Project: Configuring Access Control for Azure Storage

#### **1\. Setup Prerequisites**

*   **Software Requirements**:
    *   Install [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).
    *   Access to [Azure Portal](https://portal.azure.com).
    *   Optionally install [Azure Storage Explorer](https://azure.microsoft.com/en-us/features/storage-explorer/).
*   **Accounts and Permissions**:
    *   An active Azure subscription with permission to create and manage resources is needed.
    *   Ensure your account has at least the **Owner**, **Contributor**, or **Storage Account Key Operator Service Role**.
    *   Azure Key Vault access for secure key management (optional but recommended).

#### **2\. Best Practices**

*   **Key Management**:
    *   Use **Azure Key Vault** to manage and rotate access keys securely.
    *   Regularly rotate keys and set a key expiration policy.
*   **Authorization Methods**:
    *   Prefer **Microsoft Entra ID** (Azure AD) for data access authorization over Shared Key for superior security.
    *   Use **Shared Access Signatures (SAS)** for granular, time-limited access.
*   **Security**:
    *   Disable Shared Key authorization if not needed to prevent unintended access.
    *   Implement least privilege by assigning the minimum roles required.

#### **3\. Key Concepts**

*   **Resource Groups**: Logical containers for managing and grouping related Azure resources.
*   **Virtual Networks**: Securely connect and isolate Azure resources.
*   **Identity and Access Management (IAM)**: Use RBAC for granular access control.
*   **Shared Key Authorization**: Provides full access using generated storage account keys.
*   **Shared Access Signatures (SAS)**: Time-bound, scoped access tokens.
*   **Microsoft Entra ID**: Role-based, identity-based access.
*   **Azure Key Vault**: Securely store and manage keys, secrets, and certificates.

#### **4\. Benefits**

*   **Scalability**: Azure's infrastructure scales to handle large amounts of data.
*   **Security Features**: Built-in encryption and RBAC with Microsoft Entra ID.
*   **High Availability**: Ensures data redundancy and protection.
*   **Flexibility**: Various access and authentication options meet different security needs.

#### **5\. Complete Architecture**

**Architecture Components**:

*   **Azure Storage Account** with containers for blobs and files.
*   **Azure Virtual Machines (VMs)** for application testing.
*   **Azure Key Vault** for secure key management.
*   **Network Security Groups (NSGs)** and private endpoints for secure access.
*   **Azure AD (Microsoft Entra ID)** for identity-based access control.

#### **6\. IAM Requirements**

*   **Roles Needed**:
    *   **Owner/Contributor**: Full access to manage storage and keys.
    *   **Storage Account Key Operator Service Role**: Access to manage keys.
    *   **Storage Blob Data Contributor/Reader**: For role-based data access.
*   **Principle of Least Privilege**:
    *   Apply roles that limit access to what is required for users or services.

#### **7\. Project Objectives**

*   **Objective**: Implement a comprehensive, secure storage solution with diverse access control options.
*   **Measurable Goals**:
    *   Ensure secure access using Shared Key, SAS, Microsoft Entra ID, and optionally, on-premises AD DS.
    *   Maintain key rotation practices and key management policies using Azure Key Vault.
*   **Expected Outcomes**:
    *   An environment configured with secure, monitored access control for storage resources.
    *   Clear auditing and access management practices.

### **Implementation Steps**

#### **Step 1: Create an Azure Storage Account**

**Using Azure CLI**:

```plain
# Create a resource group 
az group create --name MyResourceGroup --location eastus

az storage account create --name mystorageaccount02025 --resource-group MyResourceGroup --location eastus --sku Standard_LRS --kind StorageV2
```

**Using Azure Portal**:

1. Go to **Storage accounts** > **Create**.
2. Fill in the details for resource group, storage account name, region, etc.
3. Click **Review + create** and Confirm.

![](https://t9014131694.p.clickup-attachments.com/t9014131694/77197f2a-ebd9-40ff-8ebe-dee13502cbc3/image.png)

#### **Step 2: Manage Account Keys**

*   **View Account Keys**:

```plain
az storage account keys list --resource-group MyResourceGroup --account-name mystorageaccount02025 --output table
```

*   **Rotate Keys**:

```plain
az storage account keys renew --resource-group MyResourceGroup --account-name mystorageaccount --key primary
```

![](https://t9014131694.p.clickup-attachments.com/t9014131694/9f50a749-51d2-47c2-88d3-aa455216c67d/image.png)

#### **Step 3: Configure Key Management with Azure Key Vault**

*   **Create a Key Vault**:

```plain
az keyvault create --name MyKeyVault02025 --resource-group MyResourceGroup --location eastus
```

*   **Add a Key to the Vault**:

```plain
az keyvault secret set --vault-name MyKeyVault02025 --name StorageAccountKey --value $ACCOUNT_KEY
```

![](https://t9014131694.p.clickup-attachments.com/t9014131694/ce87e077-a3de-43c3-8fdb-7e0301d5c4e8/image.png)

#### **Step 4: Implement Shared Access Signature (SAS)**

*   **Generate a SAS Token**:

```plain
az storage account generate-sas --account-name mystorageaccount --permissions rw --services b --resource-types co --expiry 2024-12-31 --https-only
```

#### **Step 5: Configure Microsoft Entra ID Access**

*   **Assign an IAM Role**:

```plain
az role assignment create --assignee <user-or-service-principal-id> --role "Storage Blob Data Contributor" --scope /subscriptions/<subscription-id>/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount
```

![](https://t9014131694.p.clickup-attachments.com/t9014131694/ce0806e9-1418-4488-89b0-936c8252ff80/image.png)

#### **Step 6: Set Up Key Expiration Policy**

*   **Enable Key Rotation Reminder**:
    1. In the **Azure Portal**, go to **Storage accounts** > select your storage account > **Access keys**.
    2. Click **Set rotation reminder**, enable the option, and set the frequency.
    3. Save the policy.

![](https://t9014131694.p.clickup-attachments.com/t9014131694/b934461d-7059-4d19-99cb-094b7775df76/image.png)

#### **Step 7: Monitor and Audit**

*   **Assign Built-in Policy**:
    1. Navigate to **Policy** in the Azure Portal.
    2. Search for **Storage account keys should not be expired** and assign it to your resource group.
    3. Monitor compliance under **Policy assignments**.

![](https://t9014131694.p.clickup-attachments.com/t9014131694/75ef7cb4-70f5-41eb-8ca2-f2e555c1d3a9/image.png)

###