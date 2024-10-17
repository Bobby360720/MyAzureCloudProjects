# Configure firewall settings on PaaS

### **Objective:**

To design, implement, and configure firewall settings for Azure **Platform-as-a-Service (PaaS)** resources, ensuring secure access and protecting against common web vulnerabilities while adhering to cloud security best practices.

* * *

### **1\. Understanding Security in Azure PaaS**

#### **Objective:**

Gain a deep understanding of the shared responsibility model in Azure's PaaS environment and learn how to manage security settings, including firewall configurations and other controls to secure applications deployed on PaaS.

#### **Key Concepts:**

*   **PaaS Security**: Azure provides built-in security features, but application-level security remains your responsibility.
*   **Identity as the Security Perimeter**: Shift from traditional network-based security to identity-based access controls.

#### **Security Layers**:

*   **Azure PaaS Infrastructure Security**: Managed by Microsoft.
*   **Application-Level Security**: You manage it, focusing on access control, identity management, and firewall rules.

* * *

### **2\. Plan Firewall Configurations for PaaS Resources**

#### **Objective:**

Plan firewall settings for various PaaS resources to control access and ensure only authorized users and networks can access the applications and data.

#### **Implementation Steps**:

1. **Define Resource Types**:
    *   Identify the PaaS resources that need firewall configurations, such as:
        *   **Azure App Service**
        *   **Azure SQL Database**
        *   **Azure Storage**
        *   **Azure Key Vault**
2. **Plan Access Control**:
    *   Implement the principle of **least privilege** for each resource.
    *   Determine which IP addresses and networks should be allowed or denied access.
3. **Select Firewall Type**:
    *   Use **Web Application Firewall (WAF)** for web-based PaaS services.
    *   Use **Azure DDoS Protection** to prevent denial-of-service attacks on perimeter networks.
    ```plain
    # Create the Resource Group
    az group create --name MyResourceGroup --location eastus
    # Create the Virtual Network
    az network vnet create --name MyVNet --resource-group MyResourceGroup --address-prefix 10.0.0.0/16 --location eastus
    # Create a Firewall  Subnet 
    az network vnet subnet create --resource-group MyResourceGroup --vnet-name MyVNet --name FireWallSubnet --address-prefixes 10.0.0.0/24
    # Create Public IP Address:
    az network public-ip create --resource-group MyResourceGroup --name MyPublicIp --allocation-method Static --sku Standard
    # Create A Firewall
    az network firewall create --resource-group MyResourceGroup --name MyFirewall --location eastus --conf-name MyIpConfig --vnet-name MyVNet --public-ip MyPublicIp
    # Creating a Web Application Firewall (WAF) Policy:
    az network application-gateway waf-policy create --resource-group MyResourceGroup --name MyWAFPolicy
    #  Create  DDoS Protection
    az network ddos-protection create --resource-group MyResourceGroup --name MyDdosPlan
    #  Enable  the DDoS Protection Plan with a Virtual Network
    az network vnet update --resource-group MyResourceGroup --name MyVNet --ddos-protection-plan MyDdosPlan --ddos-protection true
    ```

* * *

### **3\. Configure Firewalls on PaaS Resources**

#### **Objective:**

Implement firewall rules to protect Azure PaaS resources and limit access to authorized users and IP addresses.

#### **Tools and Technologies**:

*   **Azure Portal**
*   **Azure CLI**
*   **Azure PowerShell**

#### **Implementation Steps**:

1. **Azure App Service Firewall Configuration**:
    *   Configure **IP restriction** to control access to the App Service.
    *   Enable **Web Application Firewall (WAF)** using **Azure Application Gateway** to protect against common threats such as SQL injection and cross-site scripting (XSS).

#### **CLI Example: Configure IP Restriction for Azure App Service**:

```plain
# Create an App Service plan
az appservice plan create --name MyAppServicePlan --resource-group MyResourceGroup --sku s1 --location Canada Central

# Create an App Service (web app)
az webapp create --name MyAppService --resource-group MyResourceGroup --plan MyAppServicePlan

# Restrict access to the App Service to a specific IP range
az webapp config set --resource-group MyResourceGroup --name MyAppService --ip-security-restrictions '{"ipAddress": "192.168.0.1/24", "action": "Allow"}'
```

*    ![](https://t9014131694.p.clickup-attachments.com/t9014131694/28bc074e-43fd-4a34-a172-ad3944003c5b/image.png)
1. **Azure SQL Database Firewall Configuration**:
    *   Enable firewall rules to allow specific IP addresses to connect to the database.
    *   Implement **Azure Active Directory Authentication** to enforce identity-based access.

#### **CLI Example: Configure SQL Database Firewall Rule**:

  

```plain
# Create an Azure SQL Server
az sql server create --name MySqlServer74 --resource-group MyResourceGroup --location eastus --admin-user myadmin --admin-password MyPassword123!

# Create an Azure SQL Database
az sql db create --resource-group MyResourceGroup --server MySqlServer74 --name MyDatabase --service-objective S0

# Create a firewall rule for Azure SQL Database

az sql server firewall-rule create --resource-group MyResourceGroup --server MySqlServer74 --name AllowMyNetwork --start-ip-address 10.0.1.0--end-ip-address 10.0.1.255
```

*    ![](https://t9014131694.p.clickup-attachments.com/t9014131694/ac47963f-a90a-4d86-bf47-434ff4183e86/image.png)
*   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/5c95e8be-f79e-48b5-b522-1f33a19e35ea/image.png)
   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/f537d9a7-02c4-4cac-8e01-61178c6b9b62/image.png)
1. **Azure Storage Account Firewall Configuration**:
    *   Restrict access to storage accounts to trusted services or specific IP addresses.

#### **CLI Example: Configure Storage Account Firewall**:

```plain
# Create a storage account
az storage account create --name MyStorageAccount74 --resource-group MyResourceGroup --location eastus --sku Standard_LRS

# Connect Service Endpoints Microsoft.Storage  
az network vnet subnet update --resource-group MyResourceGroup --vnet-name MyVNet --name SQLSubnet --service-endpoints Microsoft.Storage        

# Restrict access to storage account from a specific subnet
 az storage account network-rule add -g  MyResourceGroup --account-name mystorageaccount74 --vnet-name myvnet --subnet SQLSubnet

# View storage account network-rule list
az storage account network-rule list --resource-group MyResourceGroup --account-name mystorageaccount74
```
![](https://t9014131694.p.clickup-attachments.com/t9014131694/1d36090-c3f7-4a88-9afe-ddc1daeb74fc/image.png)
    *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/2a3962c8-33e0-43bc-a70b-687cd1f19d1f/image.png)
    *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/79282140-2b2f-4f32-9cf6-e9e8c78d5241/image.png)
1. **Azure Key Vault Firewall Configuration**:
    *   Restrict access to **Azure Key Vault** using **virtual networks** and specific IP address ranges.

#### **CLI Example: Configure Key Vault Firewall Rule**:

```plain

# Add firewall rule to Azure Key Vault for a specific IP range
az keyvault network-rule add --resource-group MyResourceGroup --name MyKeyVault74 --ip-address-range 10.0.1.0/24
```

* * *

### **4\. Implement Web Application Firewall (WAF) for Application Security**

#### **Objective:**

Configure **Azure Web Application Firewall (WAF)** to protect web applications from vulnerabilities such as SQL injection and XSS attacks.

#### **Implementation Steps**:

1. **Deploy WAF with Azure Application Gateway**:
    *   Use Azure WAF to protect web apps by applying centralized security policies for web applications.
    *   Define security rules to block or allow traffic based on HTTP requests.

#### **CLI Example: Deploy WAF on Azure Application Gateway**:

```plain
# Create a WAF policy for the Application Gateway
az network application-gateway waf-policy create --resource-group MyResourceGroup --name MyWAFPolicy

# Associate WAF policy with an Application Gateway
az network application-gateway waf-policy custom-rule create --action Allow --name MyWafPolicyRule --policy-name MyWAFPolicy --priority 50 --resource-group MyResourceGroup --rule-type MatchRule
```

*   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/fb1840e9-2237-4623-98b0-1bd0ee41ebce/image.png)
 *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/ebaa2413-6865-4346-b129-fea01fdb72be/image.png)

* * *

### **5\. Monitor Firewall and Security Settings**

#### **Objective:**

Monitor firewall settings and security vulnerabilities using Azure's monitoring and alerting tools.

#### **Tools and Technologies**:

*   **Azure Monitor**
*   **Azure Security Center (Microsoft Defender for Cloud)**

#### **Implementation Steps**:

1. **Monitor Security Events**:
    *   Use **Azure Monitor** to track access logs and security events related to firewall rules and access attempts.
2. **Implement Security Alerts**:
    *   Configure security alerts in **Microsoft Defender for Cloud** to receive notifications about potential vulnerabilities or firewall misconfigurations.

#### **CLI Example: Enable Monitoring and Alerts**:

```plain
# Enable Security Center's enhanced security features
az security workspace setting create -name default  --target-workspace /subscriptions/{subscription-id}/resourcegroups/{rg}/providers/microsoft.operationalinsights/workspaces/{workspace-name}
```

   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/37d646d6-9862-487d-b0d2-cb73b338c1e4/image.png)

* * *

### **7\. Security Best Practices for PaaS Resources**

#### **Objective:**

Adopt and implement security best practices for PaaS resources to enhance overall security posture.

#### **Best Practices**:

1. **Use Azure Key Vault**:
    *   Store sensitive information like keys and secrets in **Azure Key Vault** and avoid hardcoding secrets in application code.
2. **Use Identity-Based Access Control**:
    *   Secure PaaS applications using **Azure Active Directory (Entra ID)** for authentication and authorization.
3. **Limit Access Using Least Privilege**:
    *   Apply **Role-Based Access Control (RBAC)** and IP restrictions to minimize access to PaaS resources.
4. **Regularly Review Security Settings**:
    *   Regularly review and update firewall and security settings to comply with evolving security policies.

* * *

### **8\. Perform Security Penetration Testing**

#### **Objective:**

Validate security defenses by performing regular penetration testing on PaaS resources to identify vulnerabilities.

#### **Implementation Steps**:

*   Schedule **penetration tests** on PaaS applications and infrastructure.
*   Use Azure's **Fuzz Testing** tools to test code and network endpoints.

* * *

### **Conclusion**:

In this project, you learned how to plan, configure, and monitor firewall settings on various Azure PaaS resources to enhance security. You implemented IP restrictions, set up Web Application Firewalls, enabled DDoS protection, and adopted security best practices for identity and access control. Additionally, you monitored security settings using Azure’s monitoring tools to ensure a secure and compliant environment for cloud applications.