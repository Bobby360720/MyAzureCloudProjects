# Project: Plan and Implement Network Security Groups (NSGs) and Application Security Groups (ASGs)

### **Objective:**

To plan, configure, and implement **Network Security Groups (NSGs)** and **Application Security Groups (ASGs)** to control inbound and outbound traffic to and from Azure resources within a virtual network.

* * *

### **1\. Understanding Network Security Groups (NSGs)**

#### **Objective:**

Gain an understanding of NSGs, including their security rules, properties, and how they control network traffic.

#### **Key Concepts:**

*   **Network Security Group**: A collection of rules that allow or deny traffic based on source, destination, port, and protocol.
*   **Security Rules**: Define the filtering criteria for network traffic using properties like source, destination, protocol, port range, and action (allow/deny).

#### **Implementation Steps:**

1. **Key NSG Properties**:
    *   **Name**: A unique identifier for each NSG rule.
    *   **Priority**: Determines the order in which rules are processed (lower number = higher priority).
    *   **Source/Destination**: IP addresses, service tags, or ASGs that the rule applies to.
    *   **Port/Protocol**: Specifies which port and protocol (TCP/UDP) the rule applies to.
    *   **Direction**: Defines whether the rule applies to inbound or outbound traffic.
    *   **Action**: Either allow or deny the traffic.
2. **Create NSGs Using Azure Portal**:
    *   Navigate to **Azure Portal** > **Create a Resource** > **Network Security Group**.
    *   Configure the necessary details (name, resource group, region).
    *   Create security rules to allow or deny specific traffic.

#### **CLI Example: Create an NSG and Add a Rule**:

```plain
# Create an NSG
az network nsg create --resource-group <resource-group> --name <nsg-name> --location <location>

# Add a security rule to allow inbound HTTP traffic
az network nsg rule create --nsg-name <nsg-name> --resource-group <resource-group> --name AllowHTTP --priority 100 --source-address-prefixes '*' --destination-port-ranges 80 --
protocol Tcp --access Allow --direction Inbound
```

  

   * ![](https://t9014131694.p.clickup-attachments.com/t9014131694/364e4f5c-f08d-457f-b283-eca0994e517d/image.png)
    *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/9a2f7a32-4d1b-4bcc-b051-2efc523f2152/image.png)
    *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/7db52148-baef-444e-bd2c-3c3fc2f81ad6/image.png)

* * *

### **2\. Planning Network Security Group Rules**

#### **Objective:**

Design NSG rules to control traffic to and from Azure resources, balancing security and accessibility.

#### **Tools and Technologies:**

*   **Azure Portal**
*   **Azure CLI**
*   **Azure PowerShell**

#### **Implementation Steps:**

1. **Inbound Traffic Design**:
    *   Identify the inbound traffic you need to allow (e.g., HTTP traffic from the internet to web servers).
    *   Create rules to allow this traffic, ensuring you block all unwanted traffic.
2. **Outbound Traffic Design**:
    *   Define the outbound traffic that the resources can initiate (e.g., outbound HTTP/HTTPS traffic to the internet).
    *   Allow outbound traffic for application updates, database connections, etc., while blocking unnecessary outbound traffic.
3. **Example Scenario**:
    *   Allow inbound traffic on port 80 (HTTP) and port 443 (HTTPS) from the internet.
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/774d21dc-ee50-4703-95bf-5d3fc1cca029/image.png)
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/02487626-6ef3-4613-b836-28f5b1d4ed80/image.png)

* * *

### **3\. Understanding and Implementing Application Security Groups (ASGs)**

#### **Objective:**

Learn how to use ASGs to simplify NSG rules by grouping Azure resources and applying security policies based on those groups.

#### **Key Concepts:**

*   **Application Security Group (ASG)**: Allows you to group virtual machines and other resources and apply security rules based on those groups rather than using IP addresses.

#### **Implementation Steps:**

1. **Plan ASGs Based on Application Tiers**:
    *   For example, group web servers into an ASG (e.g., `AsgWeb`), application servers into `AsgApp`, and database servers into `AsgDb`.
2. **Create ASGs**:
    *   In **Azure Portal**, navigate to **Application Security Groups** and create new ASGs based on your application's structure.
3. **Associate ASGs with NSGs**:
    *   When creating or updating NSG rules, use the ASG instead of specific IP addresses to allow or deny traffic.

#### **CLI Example: Create ASGs and Associate with NSGs**:

```plain
# Create an ASG
az network asg create --resource-group <resource-group> --name AsgWeb

# Associate ASG with NSG rule to allow HTTP traffic to web servers
az network nsg rule create --nsg-name <nsg-name> --resource-group <resource-group> --name AllowHTTP --priority 100 --source-address-prefixes '*' --destination-asg AsgWeb --destination-port-ranges 80 --protocol Tcp --access Allow --direction Inbound
```

*  ![](https://t9014131694.p.clickup-attachments.com/t9014131694/727e26b9-2653-44e3-8279-35128a6c55d6/image.png)
*   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/cecb895c-43ed-4c92-bcb3-d1bedc1d9448/image.png)
    *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/d99849ea-f1cf-40a3-807d-9c840fa57f78/image.png)

* * *

**4\. Implement Network Security Group Rules for an Application**

#### **Objective:**

Implement NSGs for your Azure resources, specifying inbound and outbound rules to secure your application.

#### **Implementation Steps:**

1. **Create NSGs for Application Resources**:
    *   Create an NSG for each application tier (e.g., web, app, and database).
    *   Associate the NSG with the appropriate subnet or network interface.
2. **Inbound Rules for Application**:
    *   **Web Tier**: Allow HTTP (port 80) and HTTPS (port 443) traffic from the internet to your web servers.
    *   **App Tier**: Allow traffic from the web tier ASG to the app tier on the necessary ports (e.g., port 8080).
    *   **Database Tier**: Only allow traffic from the app tier ASG to the database tier on port 1433 (SQL).
3. **Outbound Rules for Application**:
    *   Allow necessary outbound traffic from each tier to the internet for updates or external services.
    *   Deny all other outbound traffic.

#### **Example: Create NSG and Rule for Web Servers**:

```plain
1. Create NSGs for Application Tiers
# Create NSG for web tier
az network nsg create --resource-group <your-resource-group> --name NSG-Web

# Create NSG for app tier
az network nsg create --resource-group <your-resource-group> --name NSG-App

# Create NSG for database tier
az network nsg create --resource-group <your-resource-group> --name NSG-DB


2. Associate NSGs with Subnets or Network Interfaces
# Associate NSG-Web with the subnet for web servers
az network vnet subnet update --name WebSubnet --resource-group DTXRG --vnet-name MyVirtualNetwork  --network-security-group NSG-Web


# Associate NSG-App with the subnet for app servers
az network vnet subnet update --name AppSubnet --resource-group DTXRG --vnet-name MyVirtualNetwork  --network-security-group NSG-APD>

# Associate NSG-DB with the subnet for database servers
az network vnet subnet update --name DatabaseSubnet --resource-group DTXRG --vnet-name MyVirtualNetwork  --network-security-group NSG-DB

3. Inbound Rules for Application Tiers
Web Tier
# Allow HTTP traffic from the internet
az network nsg rule create --resource-group <your-resource-group> --nsg-name NSG-Web --name AllowHTTP --priority 100 --source-address-prefixes '*' --destination-port-ranges 80 --protocol Tcp --access Allow --direction Inbound

# Allow HTTPS traffic from the internet
az network nsg rule create --resource-group <your-resource-group> --nsg-name NSG-Web --name AllowHTTPS --priority 101 --source-address-prefixes '*' --destination-port-ranges 443 --protocol Tcp --access Allow --direction Inbound
Use code with caution.

App Tier
# Allow traffic from web tier ASG
az network nsg rule create --resource-group <your-resource-group> --nsg-name NSG-App --name AllowWebTier --priority 100 --source-asgs <web-tier-ASG-name> --destination-port-ranges <app-tier-port> --protocol Tcp --access Allow --direction Inbound
Use code with caution.

Database Tier
# Allow traffic from app tier ASG
az network nsg rule create --resource-group <your-resource-group> --nsg-name NSG-DB --name AllowAppTier --priority 100 --source-asgs <app-tier-ASG-name> --destination-port-ranges 1433 --protocol Tcp --access Allow --direction Inbound
Use code with caution.

4. Outbound Rules for Application Tiers
Allow necessary outbound traffic
# Example: Allow outbound traffic to specific IP ranges or services
az network nsg rule create --resource-group <your-resource-group> --nsg-name NSG-Web --name AllowOutbound --priority 200 --destination-address-prefixes <allowed-ip-ranges> --destination-port-ranges <allowed-ports> --protocol Tcp --access Allow --direction Outbound


Deny all other outbound traffic
az network nsg rule create --resource-group <your-resource-group> --nsg-name NSG-Web --name DenyAllOutbound --priority 300 --destination-address-prefixes '*' --destination-port-ranges '*' --protocol Tcp --access Deny --direction Outbound


Note: Replace <your-resource-group>, <your-vnet-name>, <your-subnet-name>, <NSG-Web-ID>, <NSG-App-ID>, <NSG-DB-ID>, <web-tier-ASG-name>, <app-tier-port>, and <allowed-ip-ranges> with your actual values. Adjust the priority and port ranges based on your specific requirements.
```

*  ![](https://t9014131694.p.clickup-attachments.com/t9014131694/0ffc93eb-a3a8-445c-b00a-ec426a565b09/image.png)
    *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/d7bef356-afa2-46c8-bf38-54752cc428c9/image.png)![](https://t9014131694.p.clickup-attachments.com/t9014131694/9bd092eb-3de9-4884-a896-954074635e62/image.png)![](https://t9014131694.p.clickup-attachments.com/t9014131694/90d13cac-a932-490a-ba2c-d64270272bb7/image.png)
    *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/9b6a14c9-bb22-4d1e-8845-09e482be6bdf/image.png)
    *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/b725060a-8da4-4795-8288-231fbc4d6192/image.png)
    *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/409e27ed-d3bc-4433-9013-f8643e3eb6d8/image.png)
    *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/55d7b6c6-d15a-433e-b324-5e9bc633b4dc/image.png)

* * *

### **5\. Best Practices for NSGs and ASGs**

#### **Objective:**

Ensure best practices are followed when configuring NSGs and ASGs for security, manageability, and scalability.

#### **Implementation Steps:**

1. **Minimize Rule Duplication**:
    *   Use ASGs and service tags (e.g., `Internet`, `VirtualNetwork`) to minimize the need for explicit IP addresses.
    *   Avoid duplicating rules across NSGs attached to subnets and individual network interfaces.
2. **Organize Rules by Priority**:
    *   Set priority levels carefully to avoid conflicting rules. Lower priority numbers are evaluated first.
3. **Test and Validate Rules**:
    *   Use **Azure Network Watcher** to verify traffic flows and troubleshoot rule conflicts.
    *   Check **Effective Security Rules** for a virtual machine to see how multiple NSGs are applied.
4. **Plan for Scaling**:
    *   Plan your NSGs and ASGs in a way that can easily scale with your application by grouping resources logically and avoiding IP dependencies.

* * *

### **6\. Monitor and Troubleshoot NSGs and ASGs**

#### **Objective:**

Monitor and troubleshoot traffic rules and NSG/ASG configurations to ensure they function as expected.

#### **Tools and Technologies:**

*   **Azure Network Watcher**
*   **Azure Monitor**

#### **Implementation Steps:**

1. **Use Azure Network Watcher**:
    *   Use the **IP Flow Verify** tool to check if traffic is allowed or denied by NSG rules.
    *   Use **Effective Security Rules** to see how rules are applied to a specific network interface.
2. **Monitor NSG Logs**:
    *   Enable **NSG Flow Logs** in **Network Watcher** to monitor network traffic allowed or denied by NSGs.
    *   Analyze flow logs to identify unwanted traffic and troubleshoot issues.

* * *

### **Conclusion**:

In this project, you learned how to plan, implement, and manage **Network Security Groups (NSGs)** and **Application Security Groups (ASGs)** in Azure. You configured security rules to control network traffic, associated NSGs and ASGs with resources, followed best practices, and monitored traffic using Azure Network Watcher. This allows you to secure your application while ensuring scalability and manageability.
