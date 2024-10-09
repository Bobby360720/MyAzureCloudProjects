# Project: Manage Microsoft Entra Application Access Part 2

* * *

## **Project: Manage App Registration Permission Consent**

### **Objective:**

To configure, manage, and understand the processes of delegated access, app-only access, and consent in Azure app registrations. This will involve setting permissions, understanding when admin or user consent is required, and implementing preauthorization for smooth user experiences.

* * *

### **Understand Access Scenarios for Your Application**

#### **Objective:**

Learn the two major types of access scenarios: **Delegated access** (acting on behalf of a user) and **App-only access** (acting independently of a user).

#### **Access Scenarios:**

*   **Delegated Access**: The app acts on behalf of a signed-in user and accesses resources using the user’s privileges. Both the app and the user must be authorized separately.
    *   **Example**: A web application that accesses user data (e.g., mail, calendar) using **OAuth2** delegated permissions (scopes).
*   **App-Only Access**: The app operates independently, without a user signed in, often for background processes or automation. Permissions are granted via **app roles**.
    *   **Example**: A daemon application that reads from a database or performs backups, using **application permissions** (app roles).

* * *

### **Identify the Types of Permissions Needed**

#### **Objective:**

Choose the appropriate permissions based on whether your app will use **delegated access** or **app-only access**.

#### **Types of Permissions**:

*   **Delegated Permissions**: Used for delegated access scenarios. Allows the app to act on behalf of a signed-in user, accessing only what the user can access.
    *   **Example**: If an app is granted the `Files.Read.All` delegated permission, it can only read the files that the signed-in user can access.
*   **Application Permissions**: Used for app-only access. Grants access to all data for the defined permission, not scoped to a user.
    *   **Example**: If an app is granted the `Files.Read.All` application permission, it can read **any file** in the organization, independent of a specific user.

#### **Steps to Implement**:

1. **Register the Application**:
    *   Sign in to the [Entra Admin Center](https://entra.microsoft.com).
    *   Go to **Identity** > **Applications** > **App registrations**.
    *   Create a **new registration** for your application.
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/6091659d-897e-4e51-9af8-089fdc5fd9b5/image.png)
2. **Assign Permissions**:
    *   Navigate to **API Permissions**.
    *   Select Microsoft Graph:
        *   Add either **Delegated permissions** (for delegated access) or **Application permissions** (for app-only access).
    *   Example: Choose `Files.Read.All` for delegated permissions or application permissions based on your access scenario.
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/d315fc36-8710-4c28-a3de-adb0c9d1e4a8/image.png)

* * *

### **Implement a User Consent Process**

#### **Objective:**

Understand how the **user consent** process works when an app requests permissions and how users provide their consent.

#### **Implementation Steps**:

1. **Configure the Consent Process**:
    *   In your **app registration**, go to **API permissions**.
    *   Set the permissions that will require user consent.
    *   Example: If the app accesses users’ profile information, assign [`User.Read`](http://User.Read).
2. **User Consent Flow**:
    *   When the user logs into the application for the first time, they will see a **consent prompt** asking them to approve the requested permissions.
    *   The user can either accept or reject the permissions.

#### \* ![](https://t9014131694.p.clickup-attachments.com/t9014131694/f9b2814e-4f82-4a19-b9c7-947b0b253689/image.png)

* * *

### **Implement Administrator Consent Process**

#### **Objective:**

Manage the **administrator consent** process for high-privileged or application permissions that require admin approval.

#### **Implementation Steps**:

1. **Assign High-Privilege Permissions**:
    *   In **API permissions**, assign permissions that require administrator consent (e.g., `Mail.ReadWrite` for full mailbox access).
2. **Admin Consent Flow**:
    *   When the user logs into the app, the system checks whether these permissions have been granted by an administrator.
    *   If not, the user will be prompted to contact their admin to grant consent for the app.
3. **Grant Admin Consent in Azure**:
    *   An administrator can pre-approve permissions for the entire organization.
    *   In **App registrations**, go to **API Permissions** and click **Grant admin consent for \[Tenant\]**.
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/36b42737-7df5-4181-9bb2-583a0e3dfe5c/image.png)

* * *

### **Understand Application and Service Principal Objects**

#### **Objective:**

Learn the relationship between **application objects** and **service principals**. Each application object has a service principal in every tenant where it is used.

#### **Key Concepts:**

*   **Application Object**: Created during app registration and is a global template for all tenants.
*   **Service Principal Object**: Created in each tenant where the application is used. Represents the app’s permissions and access policies in that tenant.

#### **Implementation Steps:**

1. **Application Object in Home Tenant**:
    *   In your **home tenant** (where the application was registered), navigate to **App registrations**.
    *   This represents the **global application object**.
    *   You can manage the permissions and authentication methods here.
2. **Service Principal in Consumer Tenants**:
    *   When an admin consents to an application from another tenant (for example, in a multi-tenant app), a **service principal object** is created in their tenant.
    *   Navigate to **Enterprise applications** in the **Entra Admin Center** to view the service principals associated with the app in different tenants.

### **Create and Manage Service Principals via Azure CLI**

#### **Objective:**

Use Azure CLI to create, list, and manage service principals for automation and programmatic access to Azure resources.

#### **Tools and Technologies:**

*   **Azure CLI**
*   **Microsoft Graph API**

#### **Implementation Steps:**

1. **Create a Service Principal via CLI:**

```plain
az ad sp create-for-rbac --name <your-app-name> --role Contributor --scopes /subscriptions/<subscription-id>
```

*       *   This creates both the **application object** and **service principal** in the tenant.
    *   The output will provide the `appId`, `password`, and `tenant`.
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/d9850e6e-7b49-40b6-807c-758bd60ce233/image.png)
1. **List Service Principals**: To view the service principals associated with an application:

```plain
az ad sp list --display-name <your-app-name>
```

*   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/3306f554-5429-4d53-a786-acd24dde8206/image.png)
1. **Assign Roles to Service Principals**: To assign a role to a service principal:

```plain
az role assignment create --assignee <client-id> --role <role-name> --scope /subscriptions/<subscription-id>
```

*       *   Example: Assign the **Reader** role to the service principal.
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/6addc8dd-10ae-4b3b-8164-9bdd5ffb13f3/image.png)

## **Project: Manage Managed Identities for Azure Resources**

### **Objective:**

To create, manage, and use managed identities (both system-assigned and user-assigned) in Azure resources. The project will cover assigning permissions, accessing Azure services securely using managed identities, and using Azure SDKs for token acquisition.

* * *

### **Understand Managed Identity Types**

#### **Objective:**

Understand the two types of managed identities in Azure: **system-assigned** and **user-assigned**.

#### **Key Concepts:**

*   **System-assigned Managed Identity**: Automatically created for an Azure resource (e.g., Virtual Machines, Web Apps). The managed identity's lifecycle is tied to the resource.
*   **User-assigned Managed Identity**: Created as an independent Azure resource and can be shared across multiple Azure resources.

### **Create a System-Assigned Managed Identity**

#### **Objective:**

Enable and configure a **system-assigned managed identity** for an Azure resource, such as a Virtual Machine.

#### **Tools and Technologies:**

*   **Azure Portal**
*   **Azure CLI**
*   **PowerShell**

#### **Implementation Steps:**

1. **Enable System-Assigned Managed Identity in Azure Portal:**
    *   Navigate to the **Azure Portal**.
    *   Go to **Virtual Machines** (or another supported resource).
    *   Under the **Identity** section, turn on the **System-assigned managed identity** toggle.
    *   Click **Save**.
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/cf92d113-7c6f-4542-b08c-4f2ad6b03480/image.png)
2. **Assign Permissions via RBAC:**
    *   Go to **Azure Key Vault**, **Storage Account**, or another target service.
    *   Select **Access policies** or **Role Assignments** (depending on the service).
    *   Add a role assignment to grant the VM's managed identity access to the service.
3. Example: Assign the **Key Vault Secrets User** role to the VM's managed identity to allow access to secrets in **Azure Key Vault**.
    1. ![](https://t9014131694.p.clickup-attachments.com/t9014131694/999f1a37-6ff5-4b0c-92f9-0f13d0b7ad11/image.png)

#### **CLI Example: Enable System-Assigned Managed Identity on a VM**:

```plain
bash
Copy code
az vm identity assign --name <vm-name> --resource-group <resource-group>
```

*   Replace `<vm-name>` and `<resource-group>` with your VM's details.
*   This will create a system-assigned managed identity for the VM.

  

**Create a User-Assigned Managed Identity**

#### **Objective:**

Create a **user-assigned managed identity** and assign it to multiple Azure resources.

#### **Tools and Technologies:**

*   **Azure Portal**
*   **Azure CLI**
*   **PowerShell**

#### **Implementation Steps:**

1. **Create a User-Assigned Managed Identity:**
    *   Go to **Azure Portal**.
    *   Navigate to **Managed identities** and click **Create**.
    *   Provide a name, subscription, and resource group, then click **Create**.
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/a4fbb0e7-e5ed-4148-b358-2c6fff269ab4/image.png)
2. **Assign User-Assigned Managed Identity to Resources:**
    *   Once created, assign the user-assigned managed identity to Azure resources such as Virtual Machines or App Services.
    *   Example: Assign a managed identity to a **Virtual Machine** by going to the VM's Security > **Identity** section and selecting **User-assigned**.
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/01fb9ddc-0631-4ff6-8501-ed0d768cc047/image.png)
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/54e16b78-1932-459e-8b76-609dea710a1b/image.png)

#### **CLI Example: Create and Assign User-Assigned Managed Identity**:

```plain
bash
Copy code
# Step 1: Create a user-assigned managed identity
az identity create --name <identity-name> --resource-group <resource-group> --location <region>

# Step 2: Assign the identity to a virtual machine
az vm identity assign --resource-group <resource-group> --name <vm-name> --identities <identity-id>
```

### **Authorize Managed Identity to Access a Resource (RBAC)**

#### **Objective:**

Grant the managed identity (system-assigned or user-assigned) access to an Azure resource using **Role-Based Access Control (RBAC)**.

#### **Tools and Technologies:**

*   **Azure Portal**
*   **Azure CLI**

#### **Implementation Steps:**

1. **Assign Role to Managed Identity:**
    *   Navigate to the target resource (e.g., **Azure Key Vault** or **Storage Account**).
    *   Under **Access Control (IAM)**, click **Add role assignment**.
    *   Select the managed identity and assign the desired role (e.g., **Key Vault Secrets User**, **Storage Blob Contributor**).

#### **CLI Example: Assign a Role to Managed Identity**:

```plain
bash
Copy code
az role assignment create --assignee <identity-client-id> --role "Key Vault Secrets User" --scope /subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.KeyVault/vaults/<vault-name>
```

*   Replace `<identity-client-id>` with the managed identity's client ID.
*   Replace `<subscription-id>`, `<resource-group>`, and `<vault-name>` with your Azure Key Vault details.
    *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/de336cc8-3614-40a4-a4ac-a0ef1b9dcfe8/image.png)
    *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/4214343b-2498-48c2-8cb3-81a54b074a3f/image.png)

### **Access Azure Resources Using Managed Identities**

### **Objective:**

Use the managed identity to access an Azure service, such as retrieving secrets from **Azure Key Vault**, without managing credentials.

### **Tools and Technologies:**

*   **Azure SDK (Azure.Identity Library)**
*   **Azure CLI**
*   **PowerShell**

### **Implementation Steps:**

1. **Access Azure Key Vault Using Managed Identity** (Python Example):
    *   Install the Azure SDK for Python:

```sql
  bash
  Copy code
  pip install azure-identity azure-keyvault-secrets
  
```

*       *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/275fc210-f25d-4796-96ca-93db432c7eab/image.png)
    *   *   Use the following Python code to authenticate using a managed identity and retrieve a secret from Azure Key Vault:

```plain
  python
  Copy code
  from azure.
dentity import ManagedIdentityCredential
  from azure.ke
vault.secrets import SecretClient
  
  # Define Key V
ul
 URL
  key_vault_url = "ht
ps://<your-key-vault-name>.vault.azure.net/"# Get the managed identity credential
  credential = ManagedI
entityCredential()
  
  # Create a client to i
te
act with Key Vault
  client = SecretClient(vault
url=key_vault_url, credential=credential)
  
  # Access a secret
  secret = c
ie
t.get_secret("my-se
ret-name")
  print(secret.value)
CLI Example: Us
 Azure CLI to retriev
```

1. a secret from Key Vault using the VM’s managed identity:

```dpr
bash
Copy code
az keyvault secret show --vault-name <vault-name> --name <secret-name>
```

1. If the VM's managed identity has the **Key Vault Secrets User** role, it can access the secrets without needing a stored credential.
    1. ![](https://t9014131694.p.clickup-attachments.com/t9014131694/8c379597-6a96-4e8f-87df-0f78dea41efb/image.png)

  

### **Monitor and Manage Managed Identity Activity**

#### **Objective:**

Monitor the activity of managed identities using Azure Activity logs and **Microsoft Entra** sign-in logs.

#### **Tools and Technologies:**

*   **Azure Portal**

#### **Implementation Steps:**

1. **View Managed Identity Activity Logs**:
    *   Go to **Azure Monitor** > **Activity Logs**.
    *   Filter by the managed identity to view **create**, **read**, **update**, and **delete** (CRUD) operations.
    1. ![](https://t9014131694.p.clickup-attachments.com/t9014131694/5bc43fea-7863-4916-9fb3-b08812e6d823/image.png)