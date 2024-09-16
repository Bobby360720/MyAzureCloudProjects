# Identity, Entitlement and Access Management Projects Configure Role-Based Access Control (RBAC)

**_Project Title:_**

Configure Role-Based Access Control (RBAC)

  

  

**_Objective_**

Configure RBAC roles to control access to Azure resources, ensuring that users have the appropriate permissions based on their responsibilities.

  

**Tools and Technologies Used:**

*   Azure Portal
*   Azure CLI
*   Microsoft Entra Admin Center
*   Microsoft Entra ID

**Implementation Details:**

*   **Define Access Requirements:** Identify the users, groups, or service principals needing access and define the scope (subscription, resource group, or specific resources).
*   **Assign Roles via Azure Portal:** Navigate to the resource you want to manage, select **Access Control (IAM)**, and assign the appropriate built-in role (e.g., Reader, Contributor, Owner) to users or groups.
*   **Assign Roles via Azure CLI:** Use the command `az role assignment create` to assign roles. Example:

  

Screenshots/Diagrams:

*   To assign a role, follow these steps:
    *   Ensure you are assigned the Global Administrator, User Administrator, or Privileged Role Administrator role.
    *   Locate the U**ser** using the Microsoft Entra ID.
        *   Select **Test User 2**
        *   Under the **Assigned Roles** pane
            *   **Add Assignment**
                *   Select Role: **Security Reader**
            *   Note: This role will be applied to the Directory/Tenant Scope. We don't have any other resources created at this time.
            *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/21ff563a-f438-4d93-a781-23b0b592bdee/image.png)
            *   Assignment Type: **Active**
            *   **Assigned Permanently**
            *   Enter Justification: This user needs the security **reader role** for their daily training duties as a security team member.
            *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/0be071db-055d-4ae6-99d4-31e71417386a/image.png)
            *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/c201cbd6-4907-4472-ad46-0f4fcf452ba6/image.png)
    *   Create a **Security Group** in **Microsft Entra Admin Center** and grant the group the **Helpdesk Administrator** role.
    *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/cbda0b95-0178-439f-9bef-1bb7427c8f3c/image.png)
        *   Group Type: **Security**
        *   Group Name: **N.A. Help Desk Admins**
        *   Toggle to **Yes**: Microsoft Entra roles can be assigned to the group
        *   Owner: **Test User 1**
        *   Roles: **Helpdesk Administrator**
            *   Create
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/3a6532b7-ac62-401b-894a-1b90fecdc7b6/image.png)
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/53ef3488-0e07-4b3f-a11d-4a34af8aacf9/image.png)
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/e611dcac-efdc-4045-a39c-7ee6b4752165/image.png)
    *   Assign **Test User 3** the **Reader** role at the subscription level scope
        *   az role assignment create --assignee <principal-id-or-email> --role <role-name> --scope <scope>
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/e45aec41-7c57-43fb-822a-e1890d7ea1eb/image.png)
    *   Use the `az role assignment list` command to display the user's permissions:
        *   az role assignment list --assignee [user@domain.com](mailto:user@domain.com)
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/e0e981a8-f474-4ad6-83d9-42e1d9714642/image.png)