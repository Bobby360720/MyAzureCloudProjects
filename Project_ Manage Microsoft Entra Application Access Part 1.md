# Project: Manage Microsoft Entra Application Access

### **Objective:**

To manage and configure Microsoft Entra ID (Azure AD) app registrations, permission grants, service principals, managed identities, and application proxies for secure access to enterprise applications, including OAuth permission management.

  

* * *

  

### **1\. Manage Microsoft Entra App Registrations**

### **Objectives:**

*   Create and manage app registrations for custom or third-party applications.
*   Configure permissions for these app registrations.

### **Tools and Technologies:**

*   Microsoft Entra Admin Center

### **Implementation Steps:**

1. **Create App Registration:**
    *   Go to **App registrations** in the Entra Admin Center.
    *   Click **New registration**, provide a name, and configure the **redirect URI**.
2. **Assign Roles:**
    *   Under **Roles and administrators**, assign the application-specific roles to the users or groups.
3. **Register API Permissions:**
    *   Add API permissions under **API permissions**.
    *   Choose the APIs and the specific permission scopes needed (e.g., **Microsoft Graph** permissions).

  

###   

#### **Screenshots/Diagrams:**

1. Sign in to the [Microsoft Entra admin center](https://entra.microsoft.com/) as at least a [Cloud Application Administrator](https://learn.microsoft.com/en-us/entra/identity/role-based-access-control/permissions-reference#cloud-application-administrator).
    *   Browse to **Identity** > **Applications** > **App registrations** then select **New registration**.
    *   Name the application, for example "example-app".
    *   Select a supported account type, which determines who can use the application.
    *   Under **Redirect URI**, select **Web** for the type of application you want to create. Enter the URI where the access token is sent to.
    *   Select **Register**.
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/21d77e24-0a9b-4b46-9568-ad621767aee8/image.png)
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/7e411127-de2b-405e-b8bc-f1ee331c1e6a/image.png)

  

### **2\. Configure App Registration Permission Scopes**

#### **Objectives:**

*       *   Configure and manage permission scopes for app registrations.
    *   Ensure applications have the correct access permissions to Microsoft services.

#### **Tools and Technologies:**

*       *   Microsoft Entra Admin Center
    *   Azure CLI / PowerShell

#### **Implementation Steps:**

1. Sign in to the Azure portal.
    *   Choose the scope. - **Locate your subscription**
    *   Select "Access control (IAM)."
    *   Click on "Add" and then "Add role assignment."
        *   Choose the role: **Contributor role**
        *   Click "Next."
    *   Select "Assign access to" and choose the member type.
    *   Search and select the application.
    *   Click "Review + assign.
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/9b9afd6f-4f67-41da-bed4-a397c5d67ee7/image.png)
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/307539c0-23a3-4d4c-b8c7-dcf7f9c9c347/image.png)
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/4d03f09a-8d78-4a72-bb15-b7931305fecb/image.png)
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/8688e39f-3262-4e64-b6c3-ad032ecf91f8/image.png)

3\. **Set Up Service Principal**

**Objective:**

*   Configure a service principal for programmatic authentication, which will be used in your scripts or apps to interact with Azure resources.

**Tools and Technologies:**

*   **Microsoft Entra Portal**

**Implementation Steps:**

1. **Sign In to Microsoft Entra Portal:**
    *   Go to the Portal
    *   Navigate to Microsoft Entra ID > **Enterprise Applications**.
2. **Locate Your Application:**
    *   Navigate to **App registrations** under **Identity > Applications**.
    *   Select the application associated with your service principal.
3. **Obtain Directory and Application IDs:**
    *   On the application's **Overview** page:
        *   **Copy** the **Directory (tenant) ID** and **Application (client) ID**.
        *   Store these values in your application code where you will use them for authentication.

  

**Screenshots/Diagrams:**

*     ![](https://t9014131694.p.clickup-attachments.com/t9014131694/2028ef0c-69a5-4d13-9765-8e4a479a0232/image.png)
    *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/4d2832f1-60ef-4557-82d9-082d13fe6610/image.png)

4\. **Set Up Authentication for the Service Principal**

**Objective:**

*    Configure authentication for your service principal using either a **certificate** or a **client secret**. For testing purposes, you can create a self-signed certificate.

**Tools and Technologies:**

    *   **PowerShell**
    *   **Azure Portal**
    *   **Certificate Manager (MMC)**

**Option 1: Use a Self-Signed Certificate for Testing**

1. **Create a Self-Signed Certificate in PowerShell**:
    *   Open PowerShell and run the following command to create a certificate:

```powershell
  $cert = New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature
```

1. **Export the Certificate**:
    *   Open **Run** (`Windows + R`) and type `certmgr.msc` to open the **Certificate Manager**.
    *   In the **Personal > Certificates** folder, find the certificate you just created.
    *   Right-click on the certificate and select **All Tasks > Export**.
    *   Follow the wizard to export the certificate (choose the option to export the private key if needed).
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/4b35625a-8ac4-435f-8015-adc5ef96710e/image.png)
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/946ef740-04c8-498e-ae1f-46e7511aeb82/image.png)
2. **Upload the Certificate to Azure**:
    *   In the Microsoft Entra Admin Center, go to **App registrations**, select your application, then go to **Certificates & Secrets**.
    *   Select **Certificates**, click **Upload certificate**, and upload the certificate you exported.
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/c4ac61a1-b7cf-4bcf-9310-49d93bb3a714/image.png)
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/c5dd47c5-4428-4b39-a2ce-0eb8a08b2ef8/image.png)

**Option 2: Create a Client Secret**

1. **Create a New Client Secret**:
    *   In the Microsoft Entra Admin Center, go to **App registrations**, and select your application.
    *   Navigate to **Certificates & secrets**, then click **New client secret**.
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/fbbb54a1-067d-450e-916e-1c5b9a12542a/image.png)
    *   Provide a description, choose an expiration duration, and click **Add**.
2. **Copy the Client Secret**:
    *   Once the client secret is created, **copy** the secret value. This is the only time it will be displayed.
    *   Store the client secret securely in your application code, usually alongside the **clientId** and **tenantId**.

### **Configure Access Policies on Resources (e.g., Key Vault)**

#### **Objective:**

Configure your service principal to access Azure resources like **Azure Key Vault** by setting access policies that define permissions for keys, secrets, and certificates.

#### **Tools and Technologies:**

*   **Azure Portal**
*   **Azure Key Vault**
*   **Azure CLI**

#### **Implementation Steps:**

1. **Sign in to the Azure Portal**:
    *   Go to the **Microsoft Entra Admin Center** and navigate to your **Azure Key Vault**.
2. **Set Access Policies**:
    *   In the Key Vault, go to **Access policies** and click **Add Access Policy**.
    *   Select the permissions you want to grant (e.g., for **keys**, **secrets**, or **certificates**).
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/9d2bf96f-fa4f-40ac-b2ba-ac1441e82141/image.png)
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/68c7d68e-3233-4985-a2ba-de33a769a633/image.png)
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/e7c02dfd-642e-4ca3-a077-932ba2402f56/image.png)
3. **Assign Service Principal**:
    *   Under the **Select principal** option, search for the service principal you created earlier.
    *   Select the service principal and click **Add**.
    ![](https://t9014131694.p.clickup-attachments.com/t9014131694/f3a69cdf-cfb2-45c2-8b05-f822321ce61b/image.png)
4. **Save Changes**:
    *   After adding the access policy, click **Save** to apply the changes.
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/6dc43d48-6884-4b90-a528-f4cbe6853967/image.png)

#### **Example**: Configure Key Vault Access for Secrets

If you want your service principal to access secrets from Azure Key Vault, you would set the **Secret permissions** to **Get**, **List**, and **Set**, and assign the service principal under the access policies.

  

### **Authenticate Using the Service Principal**

Once you have the **Service Principal** set up, you can authenticate programmatically using Azure CLI.

#### **\* Authenticate Using Client Secret**

Use the following command to authenticate to Azure using the **Service Principal** credentials and **client secret**:

```
az login --service-principal --username <clientId> --password <clientSecret> --tenant <tenantId>
```

*   Replace `<clientId>` with the **Application (client) ID**.
*   Replace `<clientSecret>` with the **Client Secret** generated earlier.
*   Replace `<tenantId>` with the **Directory (tenant) ID**.
    *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/ad262f7e-0c8b-49b9-8515-8467537f1d3a/image.png)

#### **Authenticate Using Certificate**

If you're using certificate-based authentication, use the following command to authenticate using the **Service Principal** and the certificate:

```
az login --service-principal --username <clientId> --tenant <tenantId> --password /path/to/certificate.pem
```

*   Replace `<clientId>` with the **Application (client) ID**.
*   Replace `<tenantId>` with the **Directory (tenant) ID**.
*   Replace `/path/to/certificate.pem` with the path to the certificate file on your machine.

## **Project: Configure App Registration Permission Scopes for a Web API**

### **Objective:**

Register a web API with Microsoft Entra, configure permission scopes, assign roles, and manage consent for client applications to securely access the web API’s resources.

* * *

### **Register the Web API with Microsoft Entra**

#### **Objective:**

To provide scoped access to your web API’s resources, register the API with the **Microsoft Identity Platform**.

#### **Tools and Technologies:**

*   Microsoft Entra Admin Center
*   Azure CLI / PowerShell

#### **Implementation Steps:**

1. **Sign in to Microsoft Entra Admin Center:**
    *   Navigate to [Entra Admin Center](https://entra.microsoft.com).
    *   Sign in as an administrator with at least **Application Administrator** or **Cloud Application Administrator** permissions.
2. **Register Your API:**
    *   Go to **Identity** > **Applications** > **App registrations**.
    *   Click **New registration** and provide a name for your API (e.g., `EmployeeRecordsAPI`).
    *   Leave the **Redirect URI** section blank (not required for APIs).
    *   Click **Register** to complete the registration.
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/a3ec7538-78f5-45b0-b507-3b40199cf0c9/image.png)
3. **Assign an Application Owner:**
    *   Under **Manage**, go to **Owners** and select **Add owners**.
    *   Add the owners who will manage the application.
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/3163694a-569c-4831-a4db-6faf7442e925/image.png)
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/addabe0c-b015-4afc-9458-e7d434d60453/image.png)

### **Assign an App Role**

#### **Objective:**

Define app roles to determine which client apps or users/groups can access the web API and what they can do.

#### **Tools and Technologies:**

*   Microsoft Entra Admin Center

#### **Implementation Steps:**

1. **Create an App Role:**
    *   In your app registration, go to **Manage** > **App roles**.
    *   Select **Create app role**.
2. **Configure the App Role:**
    *   **Display Name**: Set a display name for the role (e.g., `Employee Records Viewer`).
    *   **Allowed Member Types**: Choose **Applications** (so the role can be assigned to applications).
    *   **Value**: Set a value for the role (e.g., `Employee.Records`).
    *   **Description**: Provide a detailed description of the app role.
    *   Check the box to enable the app role, then click **Save**.
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/06562891-ec7f-4535-8f8f-a484f0c70e38/image.png)

#### **Example App Role**:

*   **Display Name**: `Employee Records Viewer`
*   **Allowed Member Types**: Applications
*   **Value**: `Employee.Records`
*   **Description**: `Applications have access to employee records`

  

### **Add a Scope to the Web API**

#### **Objective:**

Expose specific scopes to the API to define permission-based access to its resources.

#### **Tools and Technologies:**

*   Microsoft Entra Admin Center

#### **Implementation Steps:**

1. **Navigate to API Expose Settings:**
    *   In your app registration, go to **Expose an API**.
    *   Click **Add** next to **Application ID URI** if it has not been set already. You can use the default format: `api://<application-client-id>`.
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/8441ebfe-2cbd-496e-b825-96bce7c50683/image.png)
2. **Add a Scope**:
    *   Click **Add a scope**.
    *   Set the scope attributes:
        *   **Scope Name**: Name of the scope (e.g., `Employees.Read.All`).
        *   **Who Can Consent**: Choose **Admins and users** (or **Admins only** for high-privileged scopes).
        *   **Admin Consent Display Name**: A short description for admins (e.g., `Read-only access to Employee records`).
        *   **Admin Consent Description**: A more detailed description (e.g., `Allow the application to have read-only access to all Employee data`).
        *   **User Consent Display Name**: Shown to users (e.g., `Read-only access to your Employee records`).
        *   **User Consent Description**: A detailed description for users (e.g., `Allow the application to have read-only access to your Employee data`).
        *   **State**: Set to **Enabled**.
3. **Save the Scope**.

####   

*       *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/cce5e045-e94f-4f69-bd63-f66509d13728/image.png)

  

### **Add a Scope Requiring Admin Consent**

#### **Objective:**

Add a higher-privileged scope that requires admin consent for write access to the API.

#### **Tools and Technologies:**

*   Microsoft Entra Admin Center

#### **Implementation Steps:**

1. **Add a New Scope**:
    *   Follow the same steps as in **Step 3 (Add a Scope)**, but for this scope:
        *   **Scope Name**: `Employees.Write.All`
        *   **Who Can Consent**: **Admins only**
        *   **Admin Consent Display Name**: `Write access to Employee records`
        *   **Admin Consent Description**: `Allow the application to have write access to all Employee data`
2. **Save the Scope**.
*   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/bceba82a-e988-4a76-a824-dc2766b803cc/image.png)

####   

### **Verify the Exposed Scopes**

#### **Objective:**

Ensure that both read and write scopes are exposed for your API.

#### **Tools and Technologies:**

*   Microsoft Entra Admin Center

#### **Implementation Steps:**

1. **View Exposed Scopes**:
    *   Navigate to the **Expose an API** section of your app registration.
    *   Verify that both `Employees.Read.All` and `Employees.Write.All` scopes are listed.
2. **Check Scope Format**:
    *   The full scope string should be in the format of:
        *   `api://<application-client-id>/Employees.Read.All`
        *   `api://<application-client-id>/Employees.Write.All`

#### ![](https://t9014131694.p.clickup-attachments.com/t9014131694/f3fc4143-972b-4527-8e9e-80a564ab9513/image.png)