# Plan and implement an Azure Front Door, including Content Delivery Network (CDN)

### Project Overview

The project involves setting up Azure Front Door to enhance the performance, security, and scalability of an internet-facing application. This includes integrating Azure Content Delivery Network (CDN) to accelerate content delivery globally.

### Prerequisites

1. **Azure Subscription**: Ensure you have an active Azure subscription. If not, create one [here](https://azure.microsoft.com/en-us/free/).
2. **Resource Group**: Create or use an existing resource group.
3. **Application Endpoint**: An existing web application hosted in Azure or any other origin.
4. **Azure CLI Installed**: Ensure Azure CLI is installed and configured. Download [here](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli).

### Key Concepts and Best Practices

*   **Azure Front Door**: Provides global routing, SSL offload, load balancing, and application acceleration.
*   **CDN**: Caches content closer to users to reduce latency and enhance speed.
*   **SSL/TLS**: Offload SSL at the edge for performance optimization.
*   **Health Probes**: Monitor backend health and enable seamless failover.
*   **Zero Trust Security**: Incorporate secure access practices using Web Application Firewall (WAF) and DDoS protection.

### Architecture Overview

The architecture includes:

*   **Azure Front Door** for routing traffic.
*   **Web Application Firewall (WAF)** for protection.
*   **CDN** for content caching.
*   **Azure Monitor** for traffic analytics.

### Implementation Steps

#### 1\. Setup Using Azure CLI

**Step 1: Log in to Azure**

```plain
az login
```

**Step 2: Create a Resource Group**

```plain
az group create --name MyResourceGroup --location eastus
```

**Step 3: Create Azure Front Door Profile**

```plain
az afd profile create --resource-group MyResourceGroup --profile-name MyFrontDoorProfile --sku Premium_AzureFrontDoor
```

**Step 4: Create a Front Door Endpoint**

```plain
az afd endpoint create --resource-group MyResourceGroup --profile-name MyFrontDoorProfile --endpoint-name MyFrontDoorEndpoint 
```

**Step 5: Configure Custom Domain (optional)**

```plain
az afd custom-domain create --resource-group MyResourceGroup --profile-name MyFrontDoorProfile --endpoint-name MyFrontDoorEndpoint --custom-domain-name mycustomdomain.com --host-name mycustomdomain.com
```

**Step 6: Create a WAF Policy**

```plain
az network front-door waf-policy create --resource-group MyResourceGroup --name MyWAFPolicy --mode Prevention
```

**Step 7: Link WAF Policy to Front Door**

```plain
  az afd endpoint update --resource-group MyResourceGroup --profile-name MyFrontDoorProfile --endpoint-name MyFrontDoorEndpoint 



```

**Step 8: Enable Analytics**

```plain
$FRONT_DOOR_PROFILE_ID = az afd profile show --resource-group MyResourceGroup --profile-name MyFrontDoorProfile --query id -o tsv

az monitor diagnostic-settings create --name MyDiagnosticSetting --resource $FRONT_DOOR_PROFILE_ID --workspace MyLogAnalyticsWorkspace --logs '[{"category": "FrontDoorAccessLog", "enabled": true}]' --metrics '[{"category": "AllMetrics", "enabled": true}]'
```

**Step 9: Deploy and Validate** Verify the deployment by navigating to the custom domain or Azure Front Door endpoint to test latency and load balancing.

#### ![](https://t9014131694.p.clickup-attachments.com/t9014131694/46b9b42a-a5d0-4243-a83b-9cfd9239c6f0/image.png)

![](https://t9014131694.p.clickup-attachments.com/t9014131694/c7bc14a9-ed09-48a6-ac77-01fd868e1cfa/image.png)

  

![](https://t9014131694.p.clickup-attachments.com/t9014131694/1aafbff5-732d-424a-abce-7f4e6913068c/image.png)

![](https://t9014131694.p.clickup-attachments.com/t9014131694/83bf46b1-97bd-48c9-8b74-a987416c7e59/image.png)

![](https://t9014131694.p.clickup-attachments.com/t9014131694/14edac2f-fb8f-4ac5-90ed-c631d6c85c1d/image.png)

### Benefits of Azure Front Door

*   **Global Delivery Scale**: Enhanced content delivery using Microsoft's extensive global edge network.
*   **Increased Security**: Integrated WAF and DDoS protection ensure robust application security.
*   **Improved Performance**: Load balancing and SSL offloading reduce latency and boost performance.
*   **Analytics**: Real-time traffic monitoring with Azure Monitor integration.

###