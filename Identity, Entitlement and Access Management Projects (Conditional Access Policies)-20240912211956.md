# Identity, Entitlement and Access Management Projects (Conditional Access Policies)

**Project Title:**

Conditional Access Policies

**Objective:**

Implement Conditional Access Policies: Set up policies to ensure secure and compliant access to resources.

  

**Tools and Technologies Used:**

*   Azure Portal
*   Microsoft Entra Admin Center

**Objective**:

Develop and implement conditional access policies to ensure secure access based on user risk levels and device compliance, enhancing overall access security.

  

**Implementation Details:**

1. **Plan Your Conditional Access Policies**
    *   Define the security requirements for your organization (e.g., MFA, device compliance).
    *   Identify the users, groups, applications, and scenarios that need protection.
    *   Decide on the conditions that will trigger the policy (e.g., user location, device compliance, risk level).
    *   Determine the access controls you want to enforce (e.g., require MFA, block access).
2. **Access the Azure Microsoft Entra Admin Center**
    *   Go to the Azure portal: [https://portal.azure.com](https://portal.azure.com/).
    *   Navigate to **Microsft Entra ID** on the menu.
    *   In the left pane, select **Security**, then choose **Conditional Access**.
3. **Create a New Conditional Access Policy**
    *   Click on **New Policy** and provide a name for your policy.
    *   Define the **Assignments** for the policy:
    *   **Users or workload identities**: Select users or groups that this policy will apply to (e.g., All users, specific groups, or guest users).
    *   **Cloud apps or actions**: Choose the applications or actions the policy will apply to (e.g., Microsoft 365, custom apps).
    *   **Conditions**: Set conditions that determine when the policy will be applied, such as:
        *   **Sign-in risk**: Define the risk level that triggers the policy (e.g., medium and above).
        *   **Device platforms**: Specify operating systems (e.g., Windows, iOS).
        *   **Locations**: Define locations that will trigger the policy (e.g., trusted locations, specific countries).
        *   **Client apps**: Specify whether the policy applies to browser, mobile apps, or desktop clients.
4. **Configure Access Controls**
    *   Define the controls to grant or block access under **Access controls**:
        *   **Grant**: Choose the controls that need to be fulfilled to grant access (e.g., require multi-factor authentication, require compliant device).
        *   **Session**: Configure session settings if needed (e.g., app-enforced restrictions, sign-in frequency).
5. **Enable Policy and Test**
    *   Before enabling the policy, set it to **Report-only** mode to monitor how it will work without enforcing it.
    *   Review the policy and ensure it meets your requirements.
    *   Test the policy with a subset of users to validate its beh
6. **Enable the Policy**
    *   Once validated, change the policy mode from **Report-only** to **On** to enforce it.
    *   Save the policy.
7. **Monitor and Adjust**
    *   Continuously monitor the impact of your policies using the **Sign-ins** and **Audit logs** in Azure AD.
    *   Adjust conditions or access controls as needed based on user feedback and security incidents.

  

**Screenshots/Diagrams:**

*   To create a conditional access policy, follow these steps:
    *   Sign in to the **Azure Portal.**
    *   Ensure you are assigned one of the following roles: Global Administrator, Security Administrator, Conditional Access Administrator.
    *   Licenses Required for Conditional Access: Microsoft Entra ID P1 or Microsoft Entra ID P2
    *   Search for **Microsoft Entra ID**
    *   Under the **Security** tab, find the **Protect Section**, then click **Conditional Access.**
        *   Select **Create New policy**
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/56f61706-01ac-4bec-a36c-f29179237bea/image.png)
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/4b4c4010-5935-4bd1-b2ad-98054504fec4/image.png)
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/d9144622-8b1c-4058-8325-7d1466e4a4f2/image.png)
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/66708e6d-65b4-47d0-9a6b-de1212270111/image.png)
*   Create a conditional access policy that will block high-risk users
    *   Name: Block high-user risk
    *   Users: All users
        *   Exclude: (Your admin) We don't want to get locked up while testing
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/a0bf5958-cf19-40d0-9e06-86859c0cceb0/image.png)
    *   Target Resources:
        *   Cloud Apps
            *   Include
                *   All cloud apps
    *   Conditions
        *   Select and configure **User risk**
            *   Toggle configure to **Yes**
                *   select High
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/81f38dc2-e164-4d39-ae92-a9bc57b17941/image.png)
    *   Grant section
        *   Block Access
    *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/2a283f45-ce29-4725-9831-2e4c06e21dd0/image.png)
*   Enable Policy
    *   Select **Read - only**
        *   Note: Read-only will not enforce/block high-risk users. Additionally, we could grant access if the user complied with additional controls.
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/0657babe-529f-469c-8ab0-e52b370009d9/image.png)
*   You can view the newly created **policy** under the **policie**s section under **Conditional Access.**
    *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/e9777a25-ffea-4f66-8f41-4b1e670b3c80/image.png)
*    Task: Block login except from certain countries
*   To create a conditional access policy, follow these steps:
    *   Sign in to the **Microsoft Entra Admin Center.**
    *   Ensure you are assigned one of the following roles: Global Administrator, Security Administrator, Conditional Access Administrator.
    *   Licenses Required for Conditional Access: Microsoft Entra ID P1 or Microsoft Entra ID P2
    *   Under **Protection** Pane: select **Conditional Access.**
    *   **Under Manage, Named Locations**
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/e5f95de1-17a0-4baa-911c-e3606b59ec5c/image.png)
        *   *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/78657e25-ab8a-4a54-a746-43424bed96d2/image.png)
            *   Select **Countries Location**
                *   Name: **Allowed Countries**
                *   Check: **Include unknown countries/regions**
                *   Clic: **United States, Canada and Mexico**
                *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/c8e60a03-9b3f-4b29-aa2d-384abbfd504e/image.png)
            *   Create a conditional access policy to block log-ins from certain countries
                *   Under **Policies,** select **New Policy**
                *   Name: **Allowed Countries**
                1. Click **Users** > **All users**. Add any exceptions you may need.
                2. Target Resources: Click **No cloud apps, actions, or authentication contexts selected** > **All cloud apps**.
                3. Conditions: Click **0 conditions selected** > **Not configured** (under Locations) > Set **Configure** to **Yes** \> **Any location** (under Include).
                    1. Click **Exclude** > **None** (under Select) > Check **Allowed Countries** and **Multifactor authentication trusted IPs**. Click **Select**.
                    *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/bad74edc-f6b4-4d44-a513-d0c86d6ca44d/image.png)
                *   Grant:  **Block access** > **Select** \> **On** \> **Create**.
            *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/eff70e3c-d5e1-448d-889f-30b3fe1b4676/image.png)