# Identity, Entitlement and Access Management Projects (MFA)

**Project Title:**

_Multi-Factor Authentication (MFA) Rollout_

**Objective:**

Plan and implemented MFA across all critical applications and services to enhance authentication security and protect user accounts.

**Tools and Technologies Used:**

*   Azure Portal
*   Microsoft Entra Admin Center
*   Microsoft Entra ID
*   Azure MFA
*   Azure CLI

**Implementation Details:**

1. **User Provisioning**
*   **Description**: Creating and managing user accounts within the organization’s system.
*   **Tasks**:
    *   Create a User using the Portal and Azure CLI
2. **Authentication**
*   **Description**: Verifying user identities to ensure they are who they claim to be.
*   **Tasks**:
    *   Implement authentication methods such as passwords, biometric scans, or security tokens.
    *   Configure Multi-Factor Authentication (MFA) to add an extra layer of security by requiring additional verification steps.

  

**Screenshots/Diagrams:**

*   To add a new user, follow these steps:
    *   Sign in to the Azure Portal.
    *   Ensure you are assigned the Global or User Administrator Role
    *   Search For Microsft Entra ID
    *   Under the Manage tab, find the User Section, then click Add.
        *   Follow the prompts to create a new user.
        *   Review and Create
    *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/6f9ce196-3779-49c8-b23e-939841d23058/image.png)
    *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/ec47c1e3-a010-49d0-91bb-7223358ce5c7/image.png)
    *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/1d1dbe9d-fcc2-4ddc-9f07-518b0c4b0bac/image.png)
    *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/b542635b-5603-46c9-a88d-f01430f4d22b/image.png)
    *   az ad user create \\
    *   \--display-name "User2 Test" \\
    *   \--user-principal-name User2[@yourdomain.onmicrosoft.com](mailto:johndoe@yourdomain.onmicrosoft.com) \\
    *   \--password P@ssw0rd! \\
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/1d19bb32-a542-4b1a-bff6-3b05ba0d1644/image.png)
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/4e47dd03-73cd-44fb-b3e8-7c78464ea39c/image.png)
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/9dd80a43-dd74-427d-9edc-208f3916bf1d/image.png)
*   To Configure Multi-Factor Authentication (MFA), follow these steps:
*   Use the Portal
    *   Ensure you are assigned the Global or Authentication Administrator role
    *   Search For Microsft Entra ID
    *   Under the Manage tab, find the User Section, Per User MFA
    *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/0d18147a-8ec4-4604-8f2b-012d41674a3b/image.png)
    *   On the Multi-factor authentication page
        *   Select the users, then enable
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/8c0bdae1-cfbb-428b-9af7-fa9de8480d14/image.png)
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/b1615904-92a6-465f-9fea-65df2289a000/image.png)
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/a7655a03-a68a-494f-b1b0-79df4a41a217/image.png)