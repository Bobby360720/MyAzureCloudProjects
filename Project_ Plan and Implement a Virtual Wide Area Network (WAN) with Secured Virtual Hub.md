# Project: Plan and Implement a Virtual Wide Area Network (WAN) with Secured Virtual Hub

## **Project: Plan and Implement a Virtual Wide Area Network (WAN) with Secured Virtual Hub**

### **Objective:**

To design, configure, and implement a **Virtual WAN** in Azure with a **Secured Virtual Hub**. This will enable secure, high-performance connectivity between on-premises networks and Azure virtual networks using IPsec/IKE (IKEv1/IKEv2) VPN connections.

* * *

### **1\. Understanding Virtual WAN and Secured Virtual Hub**

#### **Objective:**

Learn about the **Virtual WAN** service, which connects resources in Azure and on-premises locations via VPN or ExpressRoute connections, and how to secure the network using a **Virtual Hub**.

#### **Key Concepts:**

*   **Virtual WAN**: A networking service that provides optimized, automated, and scalable branch connectivity.
*   **Virtual Hub**: A virtual network that hosts VPN gateways, ExpressRoute gateways, and firewall services for secure traffic routing.
*   **Site-to-Site VPN**: IPsec/IKE VPN connections between on-premises networks and Azure.
*   **Gateway Transit**: Allows peered virtual networks to use a shared VPN or ExpressRoute gateway.

* * *

### **2\. Plan Virtual WAN Architecture**

#### **Objective:**

Plan your **Virtual WAN** architecture, including the regions, address spaces, and virtual hubs needed to support site-to-site connectivity between Azure and on-premises resources.

#### **Implementation Steps**:

1. **Define the Network Requirements**:
    *   Determine the address space for your Virtual Hub (e.g., 10.1.0.0/16).
    *   Plan for peering multiple virtual networks to the Virtual Hub.
    *   Define the VPN or ExpressRoute connectivity between Azure and on-premises.
2. **Design a Secure Hub-and-Spoke Network Topology**:
    *   Plan a **hub-and-spoke** topology where the Virtual Hub will act as the central network, routing traffic between on-premises and Azure VNets.
    *   Plan to use **Gateway Transit** to centralize VPN or ExpressRoute gateway resources.
3. **VPN Device Setup**:
    *   Ensure you have an on-premises VPN device with an externally facing public IP address.

* * *

### **3\. Create the Virtual WAN**

#### **Objective:**

Create the **Virtual WAN** resource that will act as the central networking hub for your solution.

#### **Tools and Technologies:**

*   **Azure Portal**
*   **Azure CLI**
*   **PowerShell**

#### **Implementation Steps**:

1. **Create a Resource Group** (if not already created):
    *   In **Azure Portal**, create a resource group where your Virtual WAN and hub will reside.
2. **Create the Virtual WAN**:
    *   In **Azure Portal**, search for **Virtual WAN** and click **Create**.
    *   Specify the following:
        *   **Name**: Example: `TestVWAN1`.
        *   **Region**: Select the region (e.g., East US).
        *   **Resource Group**: Select the resource group created earlier.

#### **CLI Example: Create a Virtual WAN**:

```plain
# Create a resource group
az group create --name TestRG --location eastus

# Create the Virtual WAN
az network vwan create --name TestVWAN1 --resource-group TestRG --location eastus
```

*    ![](https://t9014131694.p.clickup-attachments.com/t9014131694/96d6d629-1786-47fa-9e04-a1eedd8ed8ff/image.png)

* * *

### **4\. Create and Configure the Virtual Hub**

#### **Objective:**

Create a **Virtual Hub** within the Virtual WAN, which will serve as the central routing point for your VPN and VNet connections.

#### **Implementation Steps**:

1. **Create the Virtual Hub**:
    *   In **Azure Portal**, go to your Virtual WAN resource and click **Create Hub**.
    *   Specify the following:
        *   **Hub Name**: Example: `Hub1`.
        *   **Address Prefix**: Example: `10.1.0.0/16`.
        *   **Region**: Select the same region as the Virtual WAN (e.g., East US).
2. **Enable Security Features in the Virtual Hub**:
    *   Enable security features like **Azure Firewall** and **Azure DDoS Protection** to secure your virtual hub.

#### **CLI Example: Create Virtual Hub**:

```plain
# Create a Virtual Hub
az network vhub create --name Hub1 --resource-group TestRG --vwan TestVWAN1 --address-prefix 10.1.0.0/16 --location eastus
```

* * *

### **5\. Create a Site-to-Site VPN Gateway**

#### **Objective:**

Create a **Site-to-Site VPN Gateway** in the Virtual Hub to securely connect on-premises resources to Azure using IPsec/IKE VPN tunnels.

#### **Implementation Steps**:

1. **Create the VPN Gateway**:
    *   In the **Azure Portal**, go to the Virtual Hub and select **VPN (Site-to-site)**.
    *   Specify the following:
        *   **VPN Gateway Name**: Example: `vpngw1`.
        *   **Scale Units**: Choose the scale units based on the bandwidth requirements.
2. **Configure the VPN Gateway**:
    *   Set up the VPN gateway with the appropriate IP address ranges, gateway settings, and VPN protocol (IKEv1/IKEv2).

#### **CLI Example: Create VPN Gateway**:

```plain
* $virtualHub = New-AzVirtualHub -VirtualWan $virtualWan -ResourceGroupName "TestRG" -Name "Hub1" -AddressPrefix "10.1.0.0/16" -Location "westus"

# Create a site-to-site VPN gateway
$virtualHub = Get-AzVirtualHub -ResourceGroupName "TestRG" -Name "Hub1"

# Create a VPN gateway using the New-AzVpnGateway cmdlet.
New-AzVpnGateway -ResourceGroupName "TestRG" -Name "vpngw1" -VirtualHubId $virtualHub.Id -VpnGatewayScaleUnit 2
```

* * *

### **6\. Create a VPN Site and Connections**

#### **Objective:**

Create **VPN Sites** that represent your on-premises locations, and configure connections between the VPN Gateway and your VPN Sites.

#### **Implementation Steps**:

1. **Create VPN Sites**:
    *   Specify the on-premises IP address ranges and public IP address of your VPN device.
    *   Create links to represent physical connections at the branch location (e.g., link speed, public IP).
2. **Create the Site-to-Site Connection**:
    *   Create connections between the VPN Gateway and the VPN Sites using the pre-shared key (PSK) for encryption.

#### **CLI Example: Create VPN Site and Connection**:

```plain
# Define address space for VPN site$vpnSiteAddressSpaces = New-Object string[] 2
$vpnSiteAddressSpaces[0] = "192.168.2.0/24"$vpnSiteAddressSpaces[1] = "192.168.3.0/24"# Create VPN site
az network vpn-site create --resource-group TestRG --name TestSite1 --address-space $vpnSiteAddressSpaces --ip-address "15.25.35.45"# Create a VPN connection
az network vpn-connection create --resource-group TestRG --name TestConnection --vhub-name Hub1 --vpn-site TestSite1 --shared-key MySharedKey

# create sites that correspond to your physical locations and the connections. These sites contain your on-premises VPN device endpoints
$vpnGateway = Get-AzVpnGateway -ResourceGroupName "TestRG" -Name "vpngw1"
$vpnSiteAddressSpaces = New-Object string[] 2
$vpnSiteAddressSpaces[0] = "192.168.2.0/24"
$vpnSiteAddressSpaces[1] = "192.168.3.0/24"

# Create links to add information about the physical links at the branch including metadata about the link speed, link provider name, and the public IP address of the on-premises device.
  $vpnSiteLink1 = New-AzVpnSiteLink -Name "TestSite1Link1" -IpAddress "15.25.35.45" -LinkProviderName "SomeTelecomProvider" -LinkSpeedInMbps "10"
  $vpnSiteLink2 = New-AzVpnSiteLink -Name "TestSite1Link2" -IpAddress "15.25.35.55" -LinkProviderName "SomeTelecomProvider2" -LinkSpeedInMbps "100"

# Create the VPN site using the New-AzVpnSite cmdlet.
$vpnSite = New-AzVpnSite -ResourceGroupName "TestRG" -Name "TestSite1" -Location "westus" -VirtualWan $virtualWan -AddressSpace $vpnSiteAddressSpaces -DeviceModel "SomeDevice" -DeviceVendor "SomeDeviceVendor" -VpnSiteLink @($vpnSiteLink1, $vpnSiteLink2)

# Create the site link connection. The connection is composed of two active-active tunnels from a branch/site to the scalable gateway.



```

* * *

### **7\. Connect a VNet to the Virtual Hub**

#### **Objective:**

Connect an existing Azure Virtual Network (VNet) to the Virtual Hub to enable communication between Azure VNets and on-premises networks.

#### **Implementation Steps**:

1. **Create a Virtual Network**:
    *   If not already created, create a VNet using the **Azure Portal** or **CLI**.
2. **Connect the VNet to the Virtual Hub**:
    *   In the Virtual Hub, create a VNet connection and specify the remote VNet to connect to the hub.

#### **CLI Example: Connect a VNet to a Virtual Hub**:

```plain
$virtualWan = Get-AzVirtualWAN -ResourceGroupName "TestRG" -Name "TestVWAN1"
$vpnGateway = Get-AzVpnGateway -ResourceGroupName "TestRG" -Name "vpngw1"
$vpnSite = Get-AzVpnSite -ResourceGroupName "TestRG" -Name "TestSite1"

# Connect the VPN site to the hub.
New-AzVpnConnection -ResourceGroupName $vpnGateway.ResourceGroupName -ParentResourceName $vpnGateway.Name -Name "testConnection" -VpnSite $vpnSite -VpnSiteLinkConnection @($vpnSiteLinkConnection1, $vpnSiteLinkConnection2)


# Create a VNet
$vnet = @{ 
    Name = 'VNet1'; 
    ResourceGroupName = 'TestRG'; 
    Location = 'eastus'; 
    AddressPrefix = '10.21.0.0/16' 
}

$virtualNetwork = New-AzVirtualNetwork @vnet

# Specify subnet settings
$subnet = @{
    Name = 'Subnet-1'; 
    AddressPrefix = '10.21.0.0/24'
}

$virtualNetwork | Add-AzVirtualNetworkSubnetConfig -Name $subnet.Name -AddressPrefix $subnet.AddressPrefix
# Set the VNet
$virtualNetwork | Set-AzVirtualNetwork

# Connect a VNet to a hub
Create a connection to peer the virtual network to the virtual hub.New-AzVirtualHubVnetConnection -ResourceGroupName "TestRG" -VirtualHubName "Hub1" -Name "VNet1-connection" -RemoteVirtualNetwork $remoteVirtualNetwork
```

*   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/ccd9fcdf-be47-43a8-a171-658a6b95ccab/image.png)
*   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/134cbcef-9ea4-4d04-951b-e4120d0dad4a/image.png)
*   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/2cd41340-ba57-4ad1-a23f-20d30bba5e03/image.png)
*   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/d8646e24-914b-4f7e-a811-4e940f8ff905/image.png)

* * *

### **8\. Download and Apply VPN Device Configuration**

#### **Objective:**

Download the VPN configuration file from Azure and apply it to your on-premises VPN device for secure Site-to-Site VPN communication.

#### **Implementation Steps**:

1. **Download VPN Configuration**:
    *   In **Azure Portal**, go to the **VPN (Site-to-Site)** page in the Virtual Hub.
    *   Click **Download VPN Config** to get the configuration settings for your VPN device.
2. **Apply VPN Configuration**:
    *   Use the configuration file to configure your on-premises VPN device. Follow the vendor's instructions to ensure the correct configuration of IPsec/IKE settings.
    *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/e4e28825-7ab9-479d-b4cd-d404bbec00e8/image.png)

* * *

### **9\. Monitor and Manage Virtual WAN and VPN Connections**

#### **Objective:**

Monitor the performance and health of your Virtual WAN, VPN Gateway, and site-to-site connections.

#### **Tools and Technologies**:

*   **Azure Monitor**
*   **Azure Network Watcher**

#### **Implementation Steps**:

1. **Monitor VPN Connection Health**:
    *   Use **Azure Monitor** to track VPN connection status, bandwidth usage, and tunnel health.
2. **Network Watcher Diagnostics**:
    *   Use **Azure Network Watcher** to run diagnostics and troubleshoot any network or connectivity issues.

* * *

### **Conclusion**:

In this project, you learned how to plan, implement, and manage an **Azure Virtual WAN** with a \*\*Secured Virtual Hub