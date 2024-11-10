# Project Plan: Implementing an Azure Application Gateway

### Objectives:

*   Deploy an Azure Application Gateway to manage and balance web traffic.
*   Configure listeners, routing rules, and backend pools.
*   Implement HTTP settings, health probes, and connection draining for efficient traffic handling.
*   Optimize the setup for security, scalability, and performance.

### Key Concepts:

*   **Azure Application Gateway**: A Layer 7 load balancer designed for web traffic management.
*   **Listeners**: Entities that listen for incoming requests and pass them to the backend pool.
*   **Routing Rules**: Rules that determine how traffic is routed to backend pools.
*   **Backend Pool**: A group of servers that receive traffic from the gateway.
*   **HTTP Settings**: Configuration for how requests are sent to the backend.
*   **Health Probes**: Used to check the health of backend servers.

### Benefits:

*   **Advanced Load Balancing**: Routes traffic based on URL paths, host headers, and more.
*   **Security**: Supports TLS termination and Web Application Firewall (WAF).
*   **Scalability**: Auto-scaling for changing traffic needs.
*   **Customizable Traffic Management**: Offers cookie-based session affinity, URL rewrites, and redirection.

### Prerequisites:

*   **Azure Subscription**: Ensure access with sufficient permissions.
*   **Resource Group**: Create a resource group to host your resources.
*   **Virtual Network (VNet)**: A VNet with subnets for the Application Gateway and backend pools.
*   **Public/Private IP**: Depending on the gateway type (internal or internet-facing).
*   **Azure CLI**: Installed and updated to the latest version.

### Best Practices:

*   **Use Separate Subnets**: Deploy the Application Gateway in its own subnet for better network management.
*   **Enable WAF**: Add Web Application Firewall for enhanced security.
*   **Set Up Health Probes**: Use custom probes for better monitoring of backend services.
*   **Optimize HTTP Settings**: Configure end-to-end TLS for secure communication.
*   **Monitor and Log**: Use Azure Monitor for real-time insights and diagnostics.

### Implementation Steps:

#### Step 1: Set Up Prerequisites

**Using Azure CLI**:

1. **Create a Resource Group**:

```plain
az group create --name MyResourceGroup --location eastus
```

1. **Create a Virtual Network and Subnets**:

```plain
az network vnet create --resource-group MyResourceGroup --name MyVNet --address-prefix 10.0.0.0/16 --subnet-name AppGatewaySubnet --subnet-prefix 10.0.1.0/24
```

1. **Create a Public IP Address**:

```plain
az network public-ip create --resource-group MyResourceGroup --name MyAppGatewayPublicIP --sku Standard --allocation-method Static
```

**Using the Azure Portal**:

1. Navigate to **Resource Groups** and create a new resource group.
2. Go to **Virtual** \> **Create** and add subnets.
3. Create a **Public IP address** from **Public IP addresses**.

  

![](https://t9014131694.p.clickup-attachments.com/t9014131694/9f42fad0-1f6d-43c0-a45a-eb5662b722e7/image.png)

![](https://t9014131694.p.clickup-attachments.com/t9014131694/f53abe99-5ce5-4c48-a74c-459b00e11a3c/image.png)

#### Step 2: Deploy the Azure Application Gateway

**Using Azure CLI**:

```plain
az network application-gateway create --resource-group MyResourceGroup --name MyAppGateway --sku Standard_v2 --capacity 2 --vnet-name MyVNet --subnet AppGatewaySubnet --public-ip-address MyAppGatewayPublicIP --http-settings-cookie-based-affinity Enabled --http-settings-protocol Http --frontend-port 80 --routing-rule-type Basic --priority 100
```

**Using the Azure Portal**:

1. Go to **Create a resource** > **Networking** > **Application Gateway**.
2. Configure the SKU, public IP, and subnet.
3. Create the gateway and ensure it’s in the same location as your VNet.

![](https://t9014131694.p.clickup-attachments.com/t9014131694/7af90790-bdc3-4216-a906-8ba1ae2db85b/image.png)

#### Step 3: Configure Listeners

**Using Azure CLI**:

```plain
az network application-gateway http-listener create --gateway-name MyAppGateway --resource-group MyResourceGroup --name MyHttpListener --frontend-port 80 --frontend-ip MyAppGatewayPublicIP
```

**Using the Azure Portal**:

1. Select **Listeners** under your Application Gateway.
2. Add a listener with the desired port and protocol.

![](https://t9014131694.p.clickup-attachments.com/t9014131694/a3aa66b0-6ed6-41f5-8bba-1d9f6c637cca/image.png)

#### Step 4: Create Backend Pools

**Using Azure CLI**:

```plain
# Create a subnet 
  az network vnet subnet create  --resource-group MyResourceGroup --vnet-name MyVNet --address-prefix 10.0.0.0/16 --name MySubnet --subnet-prefix 10.0.2.0/24

# Create Backend Servers
az vm create `
  --resource-group MyResourceGroup `
  --name BackendServer1 `
  --image Win2022Datacenter `
  --admin-username azureuser `
  --admin-password MyP@ssw0rd123 `
  --vnet-name MyVNet `
  --subnet MySubnet `
  --private-ip-address 10.0.2.4

  az vm create `
  --resource-group MyResourceGroup `
  --name BackendServer2 `
  --image Win2022Datacenter `
  --admin-username azureuser `
  --admin-password MyP@ssw0rd123 `
  --vnet-name MyVNet `
  --subnet MySubnet `
  --private-ip-address 10.0.2.5

#  Create Backend Pools and Add the Servers
az network application-gateway address-pool create --resource-group MyResourceGroup --gateway-name MyAppGateway --name MyBackendPool --servers <BackendIP1> <BackendIP2>
```

**Using the Azure Portal**:

1. Go to **Backend pools** and create a new backend pool.
2. Add the IP addresses or FQDNs of your backend servers.

![](https://t9014131694.p.clickup-attachments.com/t9014131694/0b78f30f-dcee-48f7-b8dd-217479718296/image.png)

#### Step 5: Set Up HTTP Settings

**Using Azure CLI**:

```plain
az network application-gateway http-settings create --resource-group MyResourceGroup --gateway-name MyAppGateway --name MyHttpSettings --port 80 --protocol Http --cookie-based-affinity Enabled
```

**Using the Azure Portal**:

1. Navigate to **HTTP settings** and create new settings.
2. Configure the port, protocol, and optional backend path overrides.

![](https://t9014131694.p.clickup-attachments.com/t9014131694/66d771b6-54b9-4070-976e-5316bdcd00e9/image.png)

#### Step 6: Configure Health Probes

**Using Azure CLI**:

```plain
az network application-gateway probe create --resource-group MyResourceGroup --gateway-name MyAppGateway --name MyHealthProbe --protocol Http --host <BackendFQDN> --path /health --interval 30 --timeout 30 --threshold 3
```

**Using the Azure Portal**:

1. Select **Health probes** under **Settings**.
2. Add a new probe and configure its properties.

![](https://t9014131694.p.clickup-attachments.com/t9014131694/7dfcc64d-7fc9-465c-b911-52f453a1dab4/image.png)

#### Step 7: Set Up Routing Rules

**Using Azure CLI**:

```plain
az network application-gateway rule create --resource-group MyResourceGroup --gateway-name MyAppGateway --name MyRoutingRule --http-listener appGatewayHttpListener --rule-type Basic --address-pool MyBackendPool --http-settings MyHttpSettings
```

**Using the Azure Portal**:

1. Go to **Rules** under **Settings**.
2. Create and link a rule to the listener, backend pool, and HTTP settings.

#### Step 8: Enable Web Application Firewall (Optional)

**Using Azure CLI**:

```plain
az network application-gateway waf-config set --gateway-name MyAppGateway --resource-group MyResourceGroup --enabled true --firewall-mode Prevention
```

**Using the Azure Portal**:

1. Go to **Web application firewall**.
2. Enable it and configure the rules.

#### Step 9: Monitoring and Logging

**Using Azure CLI**:

*   **Enable Diagnostic Logging**:

```plain
az monitor diagnostic-settings create --resource MyAppGatewayResourceID --name MyDiagSettings --logs '[{"category": "ApplicationGatewayAccessLog","enabled": true}]' --storage-account MyStorageAccount
```

**Using the Azure Portal**:

1. Go to **Diagnostics** under **Monitoring**.
2. Configure logs and link to a Log Analytics workspace.

### Best Practices:

*   **Enable Auto-scaling**: Use the v2 SKU for auto-scaling capabilities.
*   **Secure Traffic**: Use HTTPS listeners for encrypted data transfer.
*   **Regularly Monitor Logs**: Use Azure Monitor and Log Analytics for traffic insights.
*   **Use Custom Health Probes**: Configure probes to monitor backend health accurately.
*   **Optimize Performance**: Adjust timeouts and connection settings as needed.

### Verification:

*   Check the status and configuration using:

```sql
az network application-gateway show --resource-group MyResourceGroup --name MyAppGateway
```

*   Test access to your application through the public IP or DNS name assigned to the Application Gateway.