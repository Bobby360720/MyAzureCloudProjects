# Plan and implement network integration for Azure App Service and Azure Functions

#### Objective

Integrate Azure App Service or Azure Functions with Azure Virtual Networks (VNets) to enable secure, outbound connectivity to resources within VNets. This integration allows outbound communication from App Services/Functions to VNets while providing options for secure, controlled network traffic.

* * *

### Key Concepts

1. **VNet Integration**: Allows apps in Azure App Service and Azure Functions to make outbound requests to resources in Azure Virtual Networks.
2. **App Service Environment (ASE)**: ASEs are isolated environments, directly deployed in VNets, ideal for high-security apps needing full VNet integration.
3. **Outbound Routing Options**: Apps can route traffic to private resources in VNets, Azure services using private endpoints, or networks through ExpressRoute or VPN.

### Key Benefits

1. **Enhanced Security**: Access private resources in VNets securely without exposing apps to the internet.
2. **Network Control**: Control outbound traffic using Network Security Groups (NSGs) and User-Defined Routes (UDRs).
3. **Custom Routing**: Flexibility to configure routing for private traffic and internet-bound traffic to align with security policies.

### Best Practices

1. **Subnet Planning**: Use a /27 subnet or larger for flexibility during scaling.
2. **Traffic Control**: Apply NSGs on the integration subnet to restrict or allow traffic and use UDRs for advanced routing control.
3. **Monitoring & Troubleshooting**: Use Azure Monitor and NSG flow logs to analyze traffic patterns and detect issues.

* * *

### Step-by-Step Implementation using Azure CLI

#### Step 1: Set Up Prerequisites

1. **Create a Resource Group**:

```plain
az group create --name MyResourceGroup --location centralcanada
```

1. **Create a Virtual Network with a Subnet**:

```sql
az network vnet create `
  --resource-group MyResourceGroup `
  --name MyVNet `
  --address-prefix 10.0.0.0/16 `
  --subnet-name MySubnet `
  --subnet-prefix 10.0.1.0/24
```

1. **Assign Network Contributor Role to App Service Plan**:

```plain
az role assignment create \
  --assignee <user-email> \
  --role "Network Contributor" \
  --scope /subscriptions/<sub-id>/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet
```

#### Step 2: Create an App Service Plan and Web App

1. **Create an App Service Plan**:

```sql
az appservice plan create -g MyResourceGroup -n MyPlan --hyper-v --sku P1V3
```

1. **Create a Web App**:

```sql
az webapp create `
  --name MyWebApp720 `
  --resource-group MyResourceGroup `
  --plan MyPlan
```

![](https://t9014131694.p.clickup-attachments.com/t9014131694/5f66b979-7d3d-444e-8719-66b884610221/image.png)

#### Step 3: Configure VNet Integration

1. **Enable VNet Integration**:

```sql
az webapp vnet-integration add `
  --name MyWebApp720 `
  --resource-group MyResourceGroup `
  --vnet MyVNet `
  --subnet MySubnet
```

![](https://t9014131694.p.clickup-attachments.com/t9014131694/48552807-820f-46c7-a6e2-cc671c8687e0/image.png)

1. **Configure Outbound Traffic Settings**:

```sql
az webapp config appsettings set `
  --name MyWebApp720 `
  --resource-group MyResourceGroup `
  --settings WEBSITE_VNET_ROUTE_ALL=1
```

**\*WEBSITE\_VNET\_ROUTE\_ALL**: This setting controls how your web app routes traffic within a virtual network (VNet). Setting it to 1 enables VNet routing for all outbound traffic from your web app. This means traffic will be routed through the VNet instead of the internet directly.\*

#### Step 4: Apply NSGs and Route Tables (UDRs) for Security and Routing

1. **Create a Network Security Group**:

```sql
az network nsg create `
  --resource-group MyResourceGroup `
  --name MyNSG
```

![](https://t9014131694.p.clickup-attachments.com/t9014131694/c2c2063f-df46-4dd4-ae15-86ff71e6e60f/image.png)

1. **Add an NSG Rule to Allow Traffic**:

```plain
az network nsg rule create `
  --resource-group MyResourceGroup `
  --nsg-name MyNSG `
  --name AllowOutboundTraffic `
  --priority 100 `
  --direction Outbound `
  --access Allow `
  --protocol Tcp `
  --destination-address-prefixes * `
  --destination-port-ranges 443
```

![](https://t9014131694.p.clickup-attachments.com/t9014131694/b7adc2a7-3dce-45db-beb4-18febe947594/image.png)

1. **Associate NSG with the Subnet**:

```sql
az network vnet subnet update `
  --resource-group MyResourceGroup `
  --vnet-name MyVNet `
  --name MySubnet `
  --network-security-group MyNSG
```

  

1. Create a Firewall Subnet

```plain
az network vnet create `
    --resource-group <resource-group-name> `
    --name MyVNet `
    --address-prefix 10.0.0.0/16 `
    --subnet-name AzureFirewallSubnet `
    --subnet-prefix 10.0.2.0/26
```

![](https://t9014131694.p.clickup-attachments.com/t9014131694/5125b0b0-876e-4a84-8be9-85bda76abd5c/image.png)

1. Create Public IP

```plain
az network public-ip create `
  --resource-group MyResourceGroup `
  --name MyPublicIP `
  --sku Standard
```

1. Create a Firewall

```plain
 az network firewall create `
  --name MyFirewall `
  --resource-group MyResourceGroup `
  --location canadacentral
```

![](https://t9014131694.p.clickup-attachments.com/t9014131694/d570e2e8-0f04-4830-bd7c-f59034d89c53/image.png)

![](https://t9014131694.p.clickup-attachments.com/t9014131694/74b639d6-23d6-4066-8b29-6bcf23c8f501/image.png)

1. Associate the Public IP with the Firewall

```plain
az network firewall ip-config create `
  --firewall-name MyFirewall `
  --resource-group MyResourceGroup `
  --name MyFirewallConfig `
  --public-ip-address MyPublicIP `
  --vnet-name MyVNet
```

![](https://t9014131694.p.clickup-attachments.com/t9014131694/91172fb9-c72f-42e1-8dba-43e72a76b98f/image.png)

1. **Create a Route Table and Add a Route**:

```sql
az network route-table create `
  --resource-group MyResourceGroup `
  --name MyRouteTable

  az network route-table route create `
    --resource-group MyResourceGroup `
    --route-table-name MyRouteTable `
    --name RouteToFirewall `
    --address-prefix 0.0.0.0/0 `
    --next-hop-type VirtualAppliance `
    --next-hop-ip-address $publicIpAddress
```

![](https://t9014131694.p.clickup-attachments.com/t9014131694/663cd70c-4903-43aa-a095-4378b3e4d486/image.png)

1. **Associate Route Table with Subnet**:

```sql
az network vnet subnet update `
  --resource-group MyResourceGroup `
  --vnet-name MyVNet `
  --name MySubnet `
  --route-table MyRouteTable
```

![](https://t9014131694.p.clickup-attachments.com/t9014131694/9b2486ad-40d5-4de7-8d17-9303caae74b8/image.png)

#### Step 6: Test and Validate Connectivity

1. **Using the Azure Portal:**
*   **Application Insights:** If your App Service has Application Insights enabled, you can analyze logs to identify any network issues or errors.
 ![](https://t9014131694.p.clickup-attachments.com/t9014131694/b7e95776-7228-44a4-a04b-6afddb0b0aa8/image.png)

#### Step 7: Monitor and Troubleshoot

1. **Enable Monitoring**:

```plain
az monitor diagnostic-settings create \
  --resource "/subscriptions/<sub-id>/resourceGroups/MyResourceGroup/providers/Microsoft.Web/sites/MyWebApp" \
  --name "MyWebAppDiagnostics" \
  --logs '[{"category": "AppServiceHTTPLogs","enabled": true}]' \
  --workspace <log-analytics-workspace-id>
```

1. **Use Network Watcher for Traffic Analysis**:

```plain
az network watcher flow-log configure \
  --resource-group MyResourceGroup \
  --nsg MyNSG \
  --enabled true \
  --storage-account <storage-account-name> \
  --retention 7
```

* * *

### Conclusion

This project setup guides you through configuring Azure App Service VNet integration with outbound traffic routing, NSGs, route tables, and private endpoint options. Following these steps enhances security and networking flexibility for your App Service apps. Use Azure monitoring and logging tools to ensure secure and smooth operation across your networked environment.

###
