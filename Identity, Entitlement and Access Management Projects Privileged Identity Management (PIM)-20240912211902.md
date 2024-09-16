# Identity, Entitlement and Access Management Projects Privileged Identity Management (PIM)

**Project Title:**

_Privileged Identity Management (PIM)_

**Objective:**

Configure and manage Privileged Identity Management (PIM) to control and monitor elevated access within Azure, ensuring secure and compliant operations.

Tools and Technologies Used:

*   Microsoft Entra PIM

Roles and Privileges

*   Conditional Access Policies

Implementation Details:

1. Initial Setup: Enabled PIM in Microsoft Entra ID and configured privileged roles for administrators and key personnel.
2. Access Review: Established periodic access reviews to ensure only authorized users retain privileged access.
3. Just-in-Time (JIT) Access: Implemented JIT access for critical roles, reducing the risk of prolonged elevated access.
4. Approval Workflow: Set up approval workflows for elevated access requests, ensuring accountability and oversight.
5. Alerting and Monitoring: Configured alerts for unusual or unauthorized activity involving privileged accounts.

  

Screenshots/Diagrams:

*   Ensure that your environment has Microsoft Enterprise P2 licenses, as PIM is a feature available with this license level. Ensure that you are assigned Global Administrator or Privileged Role Administrator roles.
    *   Locate **Privileged Identity Management**.
    *   Manage Role
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/fd710369-a167-42bf-9237-12a14b3f7fa5/image.png)
    *   Add Assignment
    *   Select Resource Type
    *   Select Type: **Privileged Authentication Administrator**
    *   Scope Type: Directory
    *   Select Members
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/3be25331-95b6-4687-88b4-c5e8083fc35f/image.png)
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/2b2cfaf9-9ccf-4f82-86f6-7fb8ac465194/image.png)
        *   Assignment Type: Eligible
        *   Assignment Start and End
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/25028e60-8679-42ae-8fd8-59c34afbe8f0/image.png)
    *   Review Assignments:
        *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/356a30e5-f314-4944-951a-a823261817e2/image.png)

  

*   **Set Up Access Reviews**
1. Navigate to **Access reviews** under PIM to set up periodic reviews of role assignments.
2. Click **\+ New access review** and configure the review settings:
    *   **Review scope**: Define who will be reviewed (e.g., specific roles).
    *   **Reviewers**: Specify who will perform the review (e.g., managers, specific reviewers, or self-review).
    *   **Frequency**: Set the review frequency (e.g., one-time, weekly, quarterly).
    *   **Actions upon completion**: Define what happens when the review is complete, such as removing users who did not respond.
        *   Review Name: Privileged Authentication Administrator Review
            *   Description:
            *   Start date\*
            *   Frequency: Weekly
            *   End \* Never
            *   Scope: All users and groups
            *   Toggle: inactive users (on tenant level) only (True)
                *   Days Inactive 30
            *   Role
                *   Privileged Authentication Administrator
            *   Assignment type
                *   Active assignments only
            *   Reviewers
                *   Reviewers Manager
    *   ![](https://t9014131694.p.clickup-attachments.com/t9014131694/9b0fa63c-7124-4534-aa33-8a0d7a178366/image.png)

  

  

  

  

  

  

  

  

  

  

  

  

  

  

*   **Step 7: Monitor and Respond to Alerts**
1. **Configure Alerts**: Set up alerts to be notified of significant changes or events, such as when a role is activated outside of business hours or when a user makes multiple activation requests.
2. **Review Activity Logs**: Use Azure Monitor and the PIM activity logs to track role activations, assignments, and review completions.
3. **Respond to Alerts**: Take action based on alerts, such as revoking access or conducting further investigation.
*   **Step 8: Use Just-In-Time (JIT) Access**
4. **Activation Process**: Users assigned as eligible must activate their roles when needed through PIM.
5. **Approval Workflow**: If configured, activation may require approval from designated approvers.
6. **Time-Bound Access**: JIT access limits exposure by allowing temporary access only when needed.
*   **Step 9: Regularly Review and Adjust PIM Settings**
7. **Audit Logs**: Regularly review PIM audit logs to ensure compliance and detect any unauthorized or unusual access patterns.
8. **Adjust Roles**: Modify role settings, assignments, and review frequencies based on audit findings or changes in organizational needs.
9. **Revoke or Adjust Access**: Quickly revoke access or adjust settings if users no longer require elevated permissions.
*   **Key Benefits of PIM Configuration:**
*   **Enhanced Security**: Minimizes the risk of standing elevated access, reducing the attack surface.
*   **Compliance**: Supports regulatory requirements for access reviews and controls.
*   **Operational Efficiency**: Streamlines access management with automated processes and JIT activation.
*