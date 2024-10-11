# Project: Plan and Implement Virtual Network Peering or VPN Gateway

### **Objective:**

To connect two or more Azure Virtual Networks (VNets) using either **Virtual Network Peering** or **VPN Gateway** to enable traffic routing between virtual networks within the same region or across regions.

* * *

### **1\. Understanding Virtual Network Connection Types**

#### **Objective:**

Learn about the different connection types for connecting Azure Virtual Networks, such as **Virtual Network Peering** and **VPN Gateway**, and when to use each option.

#### **Key Concepts:**

*   **Virtual Network Peering**: Low-latency, high-bandwidth private connectivity using Microsoft’s backbone without public internet involvement.
*   **VPN Gateway**: Allows encrypted traffic between Azure VNets or between Azure and on-premises locations, typically with bandwidth limitations.

#### **Planning Considerations**:

*   **Virtual Network Peering**: Use for high-bandwidth, low-latency, private connectivity between VNets.
*   **VPN Gateway**: Use for secure, encrypted connections or when connecting to on-premises resources.

* * *

### **2\. Plan Virtual Network Peering Architecture**

#### **Objective:**

Plan the **Virtual Network Peering** architecture between two or more virtual networks, considering factors such as bandwidth, latency, and the need for private connectivity.

#### **Implementation Steps**:

1. **Define Virtual Networks**:
    *   Identify the VNets that need to be connected (same region or different regions).
    *   For each VNet, ensure there is no overlapping IP address range between the VNets.
2. **Plan the Peering**:
    *   Decide whether the VNets will be connected within the same region (local peering) or across regions (global peering).
    *   Ensure that the traffic between the peered VNets will use only private IP addresses.

#### **Scenario**:

*   **VNet1** (10.1.0.0/16) in **East US** region.
*   **VNet2** (10.2.0.0/16) in **East US** region or **West US** (for global peering).

* * *

### **3\. Implement Virtual Network Peering**

#### **Objective:**

Create and configure **Virtual Network Peering** between two virtual networks.

#### **Tools and Technologies**:

*   **Azure Portal**
*   **Azure CLI**
*   **PowerShell**

#### **Implementation Steps:**

1. **Create Virtual Networks** (if not already created):
    *   In **Azure Portal**, create two virtual networks with non-overlapping address spaces.
    *   **VNet1** with address space `10.1.0.0/16`.
    *   **VNet2** with address space `10.2.0.0/16`.
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/6c106652-1ed2-43cd-8a78-f0afdf94dff2/image.png)
2. **Create Peering Connection**:
    *   In **VNet1**, go to **Peering**, and click **Add Peering**.
    *   Configure the following:
        *   **Name**: VNet1-to-VNet2.
        *   **Peering link**: Select **VNet2** from the dropdown list.
        *   Enable **Allow forwarded traffic**, **Allow gateway transit** if applicable.
    *   Repeat the process for **VNet2** by creating the peering link **VNet2-to-VNet1**.

#### **CLI Example: Create Virtual Network Peering**:

```plain
# Create VNet1 Peering to VNet2
az network vnet peering create --name VNet1-to-VNet2 --resource-group <resource-group> --vnet-name VNet1 --remote-vnet /subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Network/virtualNetworks/VNet2 --allow-vnet-access

# Create VNet2 Peering to VNet1
az network vnet peering create --name VNet2-to-VNet1 --resource-group <resource-group> --vnet-name VNet2 --remote-vnet /subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Network/virtualNetworks/VNet1 --allow-vnet-access
```

*    ![](https://t9014131694.p.clickup-attachments.com/t9014131694/d30ba327-b5eb-44b5-8c0d-a72d7195a2ef/image.png)
    *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/609334ce-d9ed-4ef7-919f-072c5207852e/image.png)
    *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/8323a5b4-6ae9-4fb4-abcb-9b30e9519cc9/image.png)

* * *

### **4\. Plan and Implement VPN Gateway Architecture**

#### **Objective:**

Plan and configure a **VPN Gateway** for secure communication between two Azure VNets or between Azure and an on-premises network.

#### **Implementation Steps**:

1. **Identify VPN Gateway Use Case**:
    *   Use VPN Gateway when encryption is required or when connecting VNets across regions or between Azure and on-premises.
    *   Example scenario: Secure communication between **VNet1** in **East US** and **on-premises network**.
2. **Plan Gateway SKU and Connection Type**:
    *   Select the appropriate **VPN Gateway SKU** based on bandwidth and connection requirements (e.g., **VpnGw1**, **VpnGw2**).
    *   Choose between **Route-based** or **Policy-based** VPNs.

* * *

### **5\. Implement VPN Gateway between Virtual Networks**

#### **Objective:**

Create and configure a **VPN Gateway** for connecting two virtual networks or an Azure VNet to an on-premises location.

#### **Tools and Technologies**:

*   **Azure Portal**
*   **Azure CLI**
*   **PowerShell**

#### **Implementation Steps:**

1. **Create VPN Gateway for VNet1**:
    *   In **Azure Portal**, go to **Virtual Network Gateway**, and click **Create**.
    *   Choose **Vpn** as the **Gateway Type**.
    *   Configure the **SKU** and select the virtual network to attach the gateway to (e.g., VNet1).
2. **Create VPN Gateway for VNet2 or On-premises**:
    *   Repeat the above steps to create a VPN Gateway for **VNet2** or for the on-premises network.
3. **Configure VPN Gateway Connection**:
    *   Set up the connection between the VPN Gateways (or between the Azure VPN Gateway and on-premises).
    *   Configure shared keys or certificates for secure communication.

#### **CLI Example: Create VPN Gateway**:

```plain
# Create the Public IP Address (if needed):
az network public-ip create --resource-group AtlantaRG --name VNet1-Gateway-IP --allocation-method Static --sku Standard

#Create the Gateway Subnet (if needed):
az network vnet subnet create --resource-group AtlantaRG --vnet-name VNet1 --name GatewaySubnet --address-prefixes 10.1.0.0/24

# Create VPN Gateway for VNet1 and Vent2
az network vnet-gateway create --resource-group <resource-group> --name VNet1-Gateway --public-ip-address VNet1-Gateway-IP --vnet VNet1 --gateway-type Vpn --vpn-type RouteBased --sku VpnGw1

# Create VPN Gateway connection between VNet1 and VNet2
az network vpn-connection create --resource-group <resource-group> --name VNet1-to-VNet2 --vnet-gateway1 VNet1-Gateway --vnet-gateway2 VNet2-Gateway --shared-key MySharedKey
```

*    ![](https://t9014131694.p.clickup-attachments.com/t9014131694/6b9a2901-e199-4a9f-8b08-997f5a7244b6/image.png)
    *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/304455c6-7ac3-4202-89c8-8b4dd7bf601d/image.png)
    *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/4dc77491-35ca-49cf-857e-707ab14ae09c/image.png)

* * *

### **6\. Implement Gateway Transit with Peering**

#### **Objective:**

Enable **Gateway Transit** to allow shared VPN or ExpressRoute connections for multiple peered virtual networks.

#### **Implementation Steps**:

1. **Configure Peering with Gateway Transit**:
    *   When creating a peering connection, enable **Use Remote Gateway** on one side of the peering (the spoke network) and **Allow Gateway Transit** on the hub network.
2. **Enable Gateway Transit** for Peering Connections\*\*:
    *   Ensure that the shared VPN or ExpressRoute gateway is configured in the hub virtual network.
3. **Example Use Case**:
    *   Peering multiple virtual networks to a hub network that contains a single VPN Gateway to connect all virtual networks to on-premises resources.

#### **CLI Example: Enable Gateway Transit in Peering**:

```plain
#Create the HubVNet if it doesn't exist
az network vnet create --resource-group <resource group> --name HubVNet --address-prefix 10.3.0.0/16
# Enable Gateway Transit on the hub VNet
az network vnet peering update --name Hub-to-Spoke --vnet-name HubVNet --resource-group <resource-group> --allow-gateway-transit true
# Enable Use Remote Gateway on the spoke VNet
az network vnet peering update --name Spoke-to-Hub --vnet-name SpokeVNet --resource-group <resource-group> --use-remote-gateways true
```

*    ![](https://t9014131694.p.clickup-attachments.com/t9014131694/b71663e7-8c6b-4cea-9c4c-4a0f0116ae07/image.png)
    *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/56d0e7de-3e44-4bfd-a9a9-421158009a06/image.png)

* * *

### **7\. Best Practices for Virtual Network Peering and VPN Gateways**

#### **Objective:**

Ensure best practices are followed when configuring Virtual Network Peering or VPN Gateway to ensure optimal performance, security, and cost management.

#### **Best Practices**:

1. **Non-overlapping IP Ranges**: Ensure all virtual networks have non-overlapping IP address ranges to prevent routing conflicts.
2. **Monitor Network Traffic**: Use **Azure Network Watcher** to monitor traffic flow and ensure peered networks are communicating as expected.
3. **Use Private Endpoints**: For sensitive applications, ensure services are accessed using private endpoints in peered virtual networks.
4. **Consider Gateway Transit for Scalability**: Use **Gateway Transit** to centralize gateway resources for multiple peered virtual networks, reducing costs and management overhead.

* * *

### **8\. Monitor and Troubleshoot Virtual Network Peering and VPN Gateways**

#### **Objective:**

Use Azure monitoring and troubleshooting tools to verify network connectivity and troubleshoot issues with virtual network peering or VPN gateways.

#### **Tools and Technologies**:

*   **Azure Network Watcher**
*   **Azure Monitor**
*   **Log Analytics**

#### **Implementation Steps**:

1. **Use IP Flow Verify**:
    *   Use the **IP Flow Verify** tool in **Azure Network Watcher** to check whether traffic is allowed between peered virtual networks or through the VPN Gateway.
2. **Monitor VPN Gateway**:
    *   Use **Azure Monitor** to track the health and performance of the VPN Gateway, including tunnel status and traffic metrics.