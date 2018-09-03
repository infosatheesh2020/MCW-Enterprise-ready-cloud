![](https://github.com/Microsoft/MCW-Template-Cloud-Workshop/raw/master/Media/ms-cloud-workshop.png "Microsoft Cloud Workshops")

<div class="MCWHeader1">
Enterprise-ready cloud
</div>

<div class="MCWHeader2">
Hands-on lab step-on-step
</div>

<div class="MCWHeader3">
June 2018
</div>

Information in this document, including URL and other Internet Web site references, is subject to change without notice. Unless otherwise noted, the example companies, organizations, products, domain names, e-mail addresses, logos, people, places, and events depicted herein are fictitious, and no association with any real company, organization, product, domain name, e-mail address, logo, person, place or event is intended or should be inferred. Complying with all applicable copyright laws is the responsibility of the user. Without limiting the rights under copyright, no part of this document may be reproduced, stored in or introduced into a retrieval system, or transmitted in any form or by any means (electronic, mechanical, photocopying, recording, or otherwise), or for any purpose, without the express written permission of Microsoft Corporation.

Microsoft may have patents, patent applications, trademarks, copyrights, or other intellectual property rights covering subject matter in this document. Except as expressly provided in any written license agreement from Microsoft, the furnishing of this document does not give you any license to these patents, trademarks, copyrights, or other intellectual property.

The names of manufacturers, products, or URLs are provided for informational purposes only and Microsoft makes no representations and warranties, either expressed, implied, or statutory, regarding these manufacturers or the use of the products with any Microsoft technologies. The inclusion of a manufacturer or product does not imply endorsement of Microsoft of the manufacturer or product. Links may be provided to third party sites. Such sites are not under the control of Microsoft and Microsoft is not responsible for the contents of any linked site or any link contained in a linked site, or any changes or updates to such sites. Microsoft is not responsible for webcasting or any other form of transmission received from any linked site. Microsoft is providing these links to you only as a convenience, and the inclusion of any link does not imply endorsement of Microsoft of the site or the products contained therein.

© 2018 Microsoft Corporation. All rights reserved.

Microsoft and the trademarks listed at <https://www.microsoft.com/en-us/legal/intellectualproperty/Trademarks/Usage/General.aspx> are trademarks of the Microsoft group of companies. All other trademarks are property of their respective owners

**Contents** 

<!-- TOC -->

- [Enterprise-ready cloud hands-on lab step-by-step](#enterprise-ready-cloud-hands-on-lab-step-by-step)
    - [Abstract and learning objectives](#abstract-and-learning-objectives)
    - [Overview](#overview)
    - [Requirements](#requirements)
    - [Solution architecture](#solution-architecture)
    - [Exercise 1: Create the policy for Enterprise IT](#exercise-1-create-the-policy-for-enterprise-it)
        - [Help references](#help-references)
        - [Task 1: Create a Management Group](#task-1-create-a-management-group)
        - [Task 2: Apply the service catalog policy](#task-2-apply-the-service-catalog-policy)
        - [Task 3: Restrict the creation of ExpressRoute circuits](#task-3-restrict-the-creation-of-expressroute-circuits)
        - [Task 4: Restrict the creation of resources in regions](#task-4-restrict-the-creation-of-resources-in-regions)
        - [Task 5: Create and apply a naming convention](#task-5-create-and-apply-a-naming-convention)
        - [Task 6: Test the policies](#task-6-test-the-policies)
    - [Exercise 2: Configure delegated permissions](#exercise-2-configure-delegated-permissions)
        - [Help references](#help-references-1)
        - [Task 1: Create groups in Azure AD for delegation](#task-1-create-groups-in-azure-ad-for-delegation)
        - [Task 2: Create user accounts in Azure AD for delegation](#task-2-create-user-accounts-in-azure-ad-for-delegation)
        - [Task 3: Enable a business unit administrator for the subscription](#task-3-enable-a-business-unit-administrator-for-the-subscription)
        - [Task 4: Enable project-based delegation and chargeback](#task-4-enable-project-based-delegation-and-chargeback)
    - [After the hands-on lab](#after-the-hands-on-lab)
        - [Task 1: Remove resources and configuration created during this lab](#task-1-remove-resources-and-configuration-created-during-this-lab)

<!-- /TOC -->

# Enterprise-ready cloud hands-on lab step-by-step

## Abstract and learning objectives 

In this hands-on lab, you are working with Trey Research to setup some best practices regarding policies, permissions, and remote access to their network.  Tasks include creating scripts that Enterprise IT will use to automatically set policy and delegate permissions when a new subscription is created. You will help them solve a critical problem for on-boarding new developers and controlling access to what they can access on the network.

At the end of this hands-on lab, you will know how to provide cost tracking by business unit, environment and project, provide for a distributed administration model, put a service catalog in place to prevent deployment of unsupported Azure services, and put controls in place to allow deployment of services only in specific regions.

## Overview

Trey Research is a manufacturing company that builds consumer products with 29.6 billion USD in annual revenue. Trey's headquarters are in New Jersey, but they have datacenters and branch offices scattered across the United States; with several major offices in the United Kingdom, France, and Japan.

Even as large as it is, Trey seeks to maximize the cost-effectiveness and flexibility of its IT, especially in new projects and business units. With a dizzying number of existing business units; each with their own unique requirements from IT and ballooning costs from internal hardware and datacenter investment, Trey is looking to the cloud.

Trey is interested in a large-scale solution that will help mitigate creeping costs and start the transition to a modern cloud-based enterprise architecture using a solid set of controls for governance.

## Requirements

1.   Local machine or a virtual machine configured with:

        -   Visual Studio 2015 or 2017 Community Edition or VS Code

2.   Full global admin access to the Azure AD tenant associated with your Azure subscription.

## Solution architecture

![Hierarchy showing Azure AD at the root, then a Management Group, then an Azure Subscription, then resource groups, then resources. It is tagged \'Policies and RBAC\'.](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image2.png "Azure AD - Management Group - Subscription - Resource Group hierarchy")


## Exercise 1: Create the policy for Enterprise IT 

Duration: 60 minutes

In this exercise, you will first create a Management Group for your Azure subscription(s). You will apply several of the built-in Azure Policy definitions to that Management Group to ensure that users stay within the scope of supported services for Enterprise IT. Finally, you will create a new policy initiative defining a multi-resource naming convention and apply that initiative to the Management Group.

### Help references

|    |            |
|----------|:-------------:|
| Azure Policy  | <https://docs.microsoft.com/azure/azure-policy/azure-policy-introduction>|
| Azure Management Groups | <https://docs.microsoft.com/azure/azure-resource-manager/management-groups-overview>|

### Task 1: Create a Management Group

In this Task, you will create a new Management Group, and move a subscription into this Management Group. We'll later assign Azure Policy using the Management Group scope, so that it applies automatically to all subscriptions under that scope.

Note: We'll use our own Management Group, if you have permissions you could also use the Tenant Root Management Group.

1.  Launch the Azure Management Portal, and navigate to **Management Groups** under **All services**:

    ![Portal screenshot showing All Service \> Management Groups click sequence](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image4.png "Create Management Group click path")

2.  Select **New management group**, then fill in the management group ID and display name (we'll use 'ERC' as the management group ID). Leave the Parent group blank (so our new group will sit under the Tenant Root Management Group), and select **Save**

    ![Azure portal screenshot, showing New Management Group, then the group ID and name being filled in.](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image5.png "Create Management Group blade")

3.  Select on the newly-created management group, then select **Add existing**. Select 'Subscription' as the existing resource type, and choose your subscription from the drop-down list, then select **Save**.

    ![Azure portal screenshot, showing adding an existing subscription to a management group](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image6.png "Add subscription to managment group screenshot")

### Task 2: Apply the service catalog policy

In this exercise, you will apply one of the built-in Azure Policies to restrict services to the supported list provided by Trey Research.

1.  First, we need to build a list of resource types, which will be permitted, and their corresponding resource providers. We'll do that using PowerShell. Start PowerShell ISE, and log in to your Azure subscription:

```
    Login-AzureRmAccount -Subscription "{subscription name or id}"
```

2.  Enter the following script into the edit window, and run the script:

```
    $FormatEnumerationLimit = -1
    Get-AzureRmResourceProvider `
      | Select-Object ProviderNamespace, ResourceTypes `
      | Format-List
```

3.  Review the list, and identify the resource providers and resource types for each of the following:

  Resource Name
  --------------------------
-  Resource Group
-   Virtual Machines
-   Disk
-   Network Interface
-   Public IP Address
-   Network Security Group
-   Virtual Networks
-   Virtual Network Gateways
-   ExpressRoute Circuits
-   VPN Gateways
-   Storage Accounts
-   Backup Vault
-   Site Recovery Vault
-   DevTest Labs
-   Key Vault
-   Web Apps
-   SQL Database

4.  Launch the Azure Management portal, and navigate to **Policy** under **All services**:

    ![Azure portal screenshot, showing click sequence All Services, then Policy](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image7.png "Azure Policy click path screenshot")

5.  Select **Assignments**, then **Assign Policy**. Complete the form as follows:

    a.  Policy: Select 'Allowed resource types'

    b.  Name: Service Catalog policy

    c.  Description: Restrict resource types to those permitted by Enterprise IT

    d.  Assigned by: Enterprise IT

    e.  Pricing Tier: Standard

    f.  Scope: Enterprise Ready Cloud (ERC) management group, as created in Task 1

    g.  Exclusions: None

    h.  Parameters \| Allowed resource types: Choose the resource types identified in Step 3 (you may need to include some additional types, such as for NICs, Public IP Addresses, NSGs, etc.)

    The assignment form should look like this:

    ![Azure portal screenshot, showing the Assign Policy blade. The Allowed resource types policy has been chosen, and 12 resource types selected.](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image8.png "Assign Policy blade")

    When complete, select **Assign** to create the policy assignment.

### Task 3: Restrict the creation of ExpressRoute circuits

In this exercise, you will apply another built-in Azure policy to restrict the creation of ExpressRoute circuits. For this policy, we'll use an exclusion scope for the resource group in which Enterprise IT will create the permitted ExpressRoute circuits.

1.  First, we'll create the resource group for the exclusion scope. Select **Resource groups**, then **Add**, and then fill in the resource group name, select your subscription, and choose a resource group location:

    ![Azure portal screenshot, showing adding a resource group called \'ExpressRoute-group\'](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image9.png "Create resource group click path")

    Once complete, select **Create**.

2.  Return to the **Policy** blade in the Azure portal. Select **Assignments**, then **Assign Policy**. Complete the form as follows:

- Policy: Not allowed resource types

- Name: Block ExpressRoute circuits

- Description: Block creating of ExpressRoute circuits, except in the Enterprise IT dedicated ExpressRoute resource group

- Assigned by: Enterprise IT

- Pricing Tier: Standard

- Scope: Enterprise Ready Cloud (the management group created earlier)

- Exclusions: The resource group created in Step 1 above. Select the management group, subscription, and resource group.

- Parameters \| Not allowed resource types: Microsoft.Network/expressRouteCircuits

    The assignment form should look like this:

    ![Azure portal screenshot, showing the Assign Policy blade. The policy is \'Not allowed resource types\' and the ExpressRouteCircuits resource type has been selected. The policy is assigned at the Management group scope, with the ExpressRoute-group resource group as an exclusion path.](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image10.png "Assign Policy blade")

When complete, select **Assign** to create the policy assignment.

### Task 4: Restrict the creation of resources in regions 

In this exercise, you will create a new Azure Policy assignment that restricts the regions in which resources can be created in.

1.  In the Azure portal, navigate to **Policy**, then select **Assignments**, then **Assign Policy**. Complete the form as follows:

- Policy: Allowed locations

- Name: Restrict Azure locations

- Description: Restrict Azure resources to the list of Azure regions permitted by Enterprise IT

- Assigned by: Enterprise IT

- Pricing Tier: Free

- Scope: Enterprise Ready Cloud (the management group created earlier)

- Exclusions: None

- Parameters \| Allowed locations: East US, West US, North Europe, West Europe, Japan East, Japan West

    The assignment form should look like this:

    ![Azure portal screenshot, showing the Assign Policy blade. The \'Allowed locations\' policy has ben selected, with 6 locations selected.](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image11.png "Assign Policy blade")

When complete, select **Assign** to create the policy assignment.

### Task 5: Create and apply a naming convention

In this task, we will define a simple naming convention for Azure resources. We shall simply require that virtual machine names end with '-vm', virtual networks end with '-vnet', and so on across the various resource types. We will implement this naming convention using custom policy definition and policy initiative, assigned at the management group scope.

First, we shall create a generic policy definition that restricts resources of a given type to have a given name suffix. The resource type and name suffix shall be specified using parameters.

1.  In the Azure portal, open the **Policy** blade, then select **Definitions** and then **+ Policy definition**

    ![Azure portal screenshot, showing the click sequence Policy then Definitions then Add Policy Defintion](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image12.png "Add policy definition click path")

2.  Complete the Policy definition form as follows:

- Definition location: Enterprise Ready Cloud (the Management Group created earlier)

- Name: Restrict Resource Name Suffix

- Description: Restrict resources of a given type to have a name ending with a given suffix. The resource type and suffix are parameterized.

- Category: Create New, "Naming"

- Policy rule and parameters: As shown below:

    ```
        {
        "properties": {
            "mode": "all",
            "parameters": {
            "resourceType": {
                "type": "string",
                "metadata": {
                "displayName": "Resource Type",
                "description": "The resource type for this policy",
                "strongType": "resourceTypes"
                }
            },
            "nameSuffix": {
                "type": "string",
                "metadata": {
                "displayName": "Resource Name Suffix",
                "description": "The suffix that must be appended"
                }
            }
            },
            "policyRule": {
            "if": {
                "allof": [
                {
                    "field": "type",
                    "equals": "[parameters('resourceType')]"
                },
                {
                    "not": {
                    "field": "name",
                    "like": "[concat('*-', parameters('nameSuffix'))]"
                    }
                }
                ]
            },
            "then": {
                "effect": "deny"
            }
            }
        }
        }
    ```

Once the policy definition is complete, select **Save**.

Next, we shall create a policy initiative comprising multiple instances of our policy definition (one per resource type)

3.  From the **Policy** blade, on the **Definitions** panel, select **+Initiative Defintion**

4.  Fill in the Initiative Definition form as follows (but [don't]{.underline} select Save yet)

- Definition location: Enterprise Ready Cloud (the Management Group created earlier)

- Name: Naming Convention

- Description: Trey Research resource naming convention

- Category: Use Existing \| Naming

    ![Azure portal screenshot showing the \'Basics\' section of the New Policy Initiative Definition blade. The Name has been filled in as \'Naming Convention\'.](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image13.png "New Policy Definition - Basics")

5.  Under 'Available Definitions', find the 'Restrict Resource Name Suffix' policy definition created in Step 2

    ![Azure portal screenshot showing the \'Available Definitions\' section of the New Policy Initiative Definition blade. The filter has been fileed in as \'restrict\' and the results show the \'Restrict Resource Name Suffix\' policy definition](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image14.png "Available Definitions screenshot")

6.  Select the policy definition, then select **+Add** to add the Policy Definition to the Policy Initiative

    ![Azure portal screenshot, showing adding the \'restrict resource name suffix\' custom policy definition to a policy initiative](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image15.png "Adding Policy Definition to Policy Initiative")

7.  Select the resource type and name suffix. In this case, we'll choose **Microsoft.Network/virtualMachines** as the resource type and **vm** as the name suffix.

    ![Azure portal screenshot, showing filling in the parameters for the \'Restrict Resource Name Suffix\' policy definition, as part of the policy initiative definition](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image16.png "Policies and Parameters screenshot")

8.  Repeat steps 6 and 7 above for each of the resource types you wish to include in the naming convention

9.  Once you've added each resource type, select **Save**

Finally, we will apply the policy initiative across all subscriptions in the Management Group by creating an assignment at the Management Group scope.

10. On the **Policy** blade, select **Assignments** and then **Assign Initiative**.

    ![Azure portal screenshot, showing the click squence policy then Assignments then Assign Initiative.](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image17.png "Assign Initiative click path")

11. Complete the Assign Initiative form as follows:

- Initiative definition: Naming Convention (the initiative definition we just created)

- Name: Resource Naming Convention

- Description: Enforces company-wide resource naming convention

- Assigned by: Enterprise IT

- Pricing Tier: Standard

- Scope: Enterprise Ready Cloud (the Management Group created earlier)

- Exclusions: None

    The assignment form should look like this:

    ![Azure portal screenshot, showing the Assign Initiative blade. The Naming Convention policy initiative has been selected, and the assignment is at management group scope.](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image18.png "Assign Initiative Azure portal blade")

- When complete, select **Assign** to create the policy initiative assignment

### Task 6: Test the policies 

In this task, you will use the Azure management portal to validate each of the policies created so far, and understand how to identify policy events.

**Subtask 1: Test the service catalog policy**

1.  Navigate to the Azure management portal in a browser <http://portal.azure.com> and sign in

2.  Select **Create a Resource \> Internet of Things \> IoT Hub**

    ![Azure portal screenshot, showing click sequence to create an IoT Hub resource](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image19.png "Create IoT hub Azure portal click path")

3.  Specify a unique name for the IoT Hub, and choose an existing resource group. Choose a permitted location (we are only testing the Service Catalog policy at this time).

    ![Azure portal screenshot, showing the Create IoT hub blade. The name has been filled in as \'policytesthub\'.](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image20.png "Create IoT Hub Azure portal blade")

    Once all the settings have been filled in, select **Create**.

4.  The IoT Hub creation blade should show an error:

    ![Azure portal screenshot, showing error message \"There were policy errors. Click here to view details\"](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image21.png "Policy Error screenshot")

5.  Select the error. The following error details are displayed:

    ![Azure portal screenshot, showing Errors blade. The error message states the template deployment failed due to a policy violation. The error details show JSON text with the policy definition ID and policy assignment ID](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image22.png "Policy Violation error message")

**Subtask 2: Test the ExpressRoute circuit policy**

1.  Select **New** **\>** **Networking** **\>** **ExpressRoute**.

    ![Azure portal screenshot, showing click sequence to create an ExpressRoute circuit](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image23.png "Create ExpressRoute click path")

2.  Specify the following configuration for the circuit and select **Create**.

Note: you may have to specify an alternate region if West United States is not supported with your subscription.

![The Circuit configuration fields are set to the following settings: Circuit name, TestCircuit; Provider, AT&T; Peering location, Silicon Valley; Bandwidth, 50Mbps; SKU, Standard.](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image24.png "Circuit configuration fields") ![The Circuit configuration fields are set to the following settings: Billing model, Unlimited; Subscription, opsgilitytraining; Resource group, PolicyTestIRG; Location, West US.](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image25.png "Circuit configuration fields")

3.  As with the Service Catalog policy, you should see an error in the Create ExpressRoute Circuit blade, which when clicked shows the error details:

    ![Azure portal screenshot, showing error message \"There were policy errors. Click here to view details\"](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image21.png "Validation Error screenshot")

    ![Azure portal screenshot, showing Errors blade. The error message states the template deployment failed due to a policy violation. The error details show JSON text with the policy definition ID and policy assignment ID](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image26.png "Policy Violation error message")

**SubTask 3: Test the resource location policy**

1.  Testing the resource location policy follows a similar pattern. Attempt to create a permitted resource, with a permitted name, but in a not-permitted region. For example, create a virtual network named 'erc-vnet' in South Central US. This should be rejected by the 'Restrict Azure locations' policy.

2. To test further, change to a permitted location (e.g. East US) and try again---this time, the virtual network should be created OK. Note: you may need to refresh browser to release caching on policies.

**SubTask 4: Test the naming convention policy**

1. Attempt to create a permitted resource, in a permitted location, with a not-permitted name. For example, create a virtual network named 'erc-network' in East US. This should be rejected by the 'Resource Naming Convention' policy.

2. To test further, change to a permitted name (e.g. 'erc-network-vnet') and try again---this time, the virtual network should be created OK. Note: you may need to refresh browser to release caching on policies.

## Exercise 2: Configure delegated permissions

Duration: 60 minutes

In this exercise, you will configure delegated permissions for users in the Trey Research business unit. You will extend a PowerShell script to automatically provision a limited access user with the configuration of the subscription.

### Help references

|    |            |
|----------|:-------------:|
| Add new users to Active Directory | <https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory> |
| How Subscriptions are associated with Azure AD | <https://docs.microsoft.com/azure/active-directory/active-directory-how-subscriptions-associated-directory> |
| Managing Azure AD Security Groups | <https://docs.microsoft.com/azure/active-directory/active-directory-groups-create-azure-portal> |
| Role Based Access Control  | <https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure> |
| Manage RBAC with PowerShell | <https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-powershell> |


### Task 1: Create groups in Azure AD for delegation 

In this task, you will create two groups in Azure AD that you will use for testing delegated access control. You will add the users created in the previous task to the groups.

1.  Open the Azure Active Directory console under the Azure Management portal in your browser (<https://portal.azure.com>)

2.  Select **Groups**, and then select **New group**

    ![Screenshot of the New group button.](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image27.png "New group button")

3.  Specify the **Security** as the Group type and **BU-Electronics-Admin** as the **Name** and **Description**. Change the Membership type to **Assigned**. Select **Create**.

    ![Azure portal screenshot, showing New Azure AD Group blade. THe group type is security, group name and group description are BU-Electronics-Admin. Membership type is \'Assigned\', with no members assigned.](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image28.png "New Azure AD Group blade")

4.  Repeat the process, and create another group named **BU-Electronics-Users**

### Task 2: Create user accounts in Azure AD for delegation 

In this task, you will create two user accounts in Azure AD that you will use for testing delegated access control.

1.  Navigate to **All services -\> Azure Active Directory**, and select **Custom domain names** to find out the name of your Azure AD tenant (this will be needed in the next step)

    ![Azure portal screenshot, showing button \'Custom Domain Names\'](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image29.png "Custom domain names button")

2.  Select **Users**, and then select **+New user**

    ![Screenshot of the New user button.](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image30.png "New user button")

3.  Specify the following configuration for the new user:

- **Name**:    Electronics Admin                                         - **User name:** [ElectronicsAdmin@\[yourtenant\].onmicrosoft.com](mailto:ElectronicsAdmin@[yourtenant].onmicrosoft.com) 
- **Groups**: Add the user to the BU-Electronics-Admin group.     
**Password**: Check the Show password checkbox and note the password for later.     

    ![In the New User dialog box, the Name, User name, and Groups fields are circled, and set to the previously defined settings. At the bottom, the check box for Show Password is selected and circled.](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image31.png "New User dialog box") |


4.  Create a second user with the following configuration:

- **Name**: Electronics User                                             - **User name:** [ElectronicsUser@\[yourtenant\].onmicrosoft.com](mailto:ElectronicsUser@[yourtenant].onmicrosoft.com) 
- **Groups**: Add the user to the BU-Electronics-User group.             - **Password**: Check the Show password checkbox and note the password for later.
                                                
    ![In the New User dialog box, the Name, User name, and Groups fields are circled, and set to the previously defined settings. At the bottom, the check box for Show Password is selected and circled.](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image32.png "New User dialog box") |



### Task 3: Enable a business unit administrator for the subscription 

In this task, you will update a script to automatically add a user to the contributor role of the subscription.

1.  Open PowerShell ISE, and log in to your Azure account

```
    Login-AzureRmAccount
```

2.  Create a new script **ConfigureSubscription.ps1** in PowerShell ISE

3.  Add the following code to script, and save the file. This code will retrieve the object ID for the Active Directory group passed in and assign the group to the Contributor role on the subscription.

```

    param([string]$SubscriptionId, [string]$AdGroupName) 

    Select-AzureRmSubscription -SubscriptionId $SubscriptionId

    $scope = "/subscriptions/$SubscriptionId"

    $groupObjectId = (Get-AzureRmADGroup -SearchString $AdGroupName).Id.Guid

    Write-Host "Adding group to contributor role" -ForegroundColor Green

    New-AzureRmRoleAssignment -Scope $scope `
                              -RoleDefinitionName "Contributor" `
                              -ObjectId $groupObjectId 

```

This code will add an Azure AD security group to the contributor role at the subscription scope.

4.  Create a local variable containing your Subscription ID (you can copy your subscription ID from the Azure portal, or obtain it using Get-AzureRmSubscription):

    Paste this under the param section of the script and save.

```
    $SubscriptionId = "{your subscription id}"
```

5.  Execute the script passing in the -SubscriptionID and -AdGroupName parameters:

```
    .\ConfigureSubscription.ps1 -SubscriptionId $SubscriptionId -AdGroupName "BU-Electronics-Admin"
```

6.  Close all instances of your browser (or switch to a different type of browser) and re-launch In-Private or Incognito mode

7.  Navigate to the Azure management portal in a browser <http://portal.azure.com>, and sign in using the **ElectronicsAdmin** credentials created earlier. When prompted to change your password, specify a strong password you will remember.

8.  You will need to configure a method of resetting your account. You can choose either a phone call or email.

9.  Select **All services**, and then select **Subscriptions**

    ![Azure portal screenshot, showing subscriptions button](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image33.png "Subscriptions button")

10. Select the name of the subscription you have been working on

11. Select the **Access control (IAM)** tile:

    ![Azure portal screenshot, showing \'Access Control (IAM)\' button](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image34.png "Access Control button")

12. You should see the BU-Electronics-Admin group assigned to the contributor role.

    ![Under User, the BU-Electronics-Admin group is circled. It has the Role of Contributor, and Access as Assigned.](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image35.png "BU-Electronics-Admin group")

    Users in the contributor role scoped at the subscription have full access to all the resources within the subscription, but cannot grant access to others or change policies on the subscription.

### Task 4: Enable project-based delegation and chargeback

In this task, you will create a script that will create a new resource group, assign 'Owner' rights over the resource group to a given AD group, and then apply a policy to enforce an 'IOCode' tag with a given value.

1.  Using PowerShell ISE, select **File \> New**, and save the file in the **C:\\Hackathon\\ERC** folder. Name the file **CreateProjectResourceGroup.ps1**.

2.  Add the following code to the script, and **Save** the file

```
    param(
        [string]$SubscriptionId, 
        [string]$ResourceGroupName, 
        [String]$Location, 
        [String]$IOCode,
        [string]$AdGroupName
    ) 

    Select-AzureRmSubscription -SubscriptionId $SubscriptionId

    # Create resource group
    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location 

    $scope = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName"

    # Assign Owner role to given group
    $groupObjectId = (Get-AzureRmADGroup -SearchString $AdGroupName).Id.Guid

    New-AzureRmRoleAssignment -Scope $scope `
                              -RoleDefinitionName "Owner" `
                              -ObjectId $groupObjectId

    # Assign policy to apply IOCode tag
    $definition = Get-AzureRmPolicyDefinition | where {$_.Properties.displayName -eq "Apply tag and its default value"}

    $parameters = @{
        tagName = 'IOCode'
        tagValue = $IOCode
        }

    New-AzureRmPolicyAssignment -Name "AppendIOCode" `
                                -Scope $scope `
                                -DisplayName "Append IO Code" `
                                -PolicyDefinition $definition `
                                -PolicyParameterObject $parameters

```


This code creates a new resource group in the specified region. It then assigns the group to the owner role definition just for the resource group. It will allow users in the group to have full ownership of resources within the resource group only. This code applies a built-in policy to append a tag with name 'IOCode' and then applies the given tag value to any resource created in the resource group.

3.  In the **Console** pane, create a new variable called **\$location**, and specify a region name to deploy to the resource group to. This location must be one of the supported regions in your previously created policy.

```
    $location = "West US"
```

4.  In the **Console** pane, create a new variable called **\$resourceGroupName**, and specify the value as **DelegatedProjectDemo**. Also, make sure you create a **\$SubscriptionId** variable as you did earlier.

```
    $resourceGroupName = "DelegatedProjectDemo"
    $SubscriptionId = "{your subscription id}"
```

5.  In the **Console** pane, execute the following command to create a new resource group with delegated permissions and IO Code policy

```
    .\CreateProjectResourceGroup.ps1 -SubscriptionId $SubscriptionId -ResourceGroupName $resourceGroupName -Location $location -IOCode "1000150" -AdGroupName "BU-Electronics-Admin"
```

6.  Create a new storage account in the resource group (choose a unique name) to validate the ioCode tag was applied (replace uniquestorageaccount with a unique value)

```
    New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName `
                              -Name "uniquestorageaccount" -SkuName Standard_LRS `
                              -Location $location 
```

You should see the ioCode tag applied in the output.

![Screenshot of the Console Pane displaying the following line of code: Tags : {\[ioCode, 1000150\]}](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image36.png "Console Pane code")

7.  Switch back to the Azure Management portal using the ElectronicsAdmin credentials

8.  Select **Resource Groups**

9.  Select the **DelegatedProjectdemo** resource group

    ![Screenshot of the DelegatedProjectdemo resource group.](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image37.png "DelegatedProjectdemo resource group")

10. Select the **Access** icon

    ![Screenshot of the Access icon.](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image38.png "Access icon")

11. Note that the BU-Electronics-Admin role is set as the owner of the resource group

    ![Under User, the BU-Electronics-Admin group now has the Role of Owner, Contributor, which is circled.](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image39.png "Owner, Contributor permissions")

12. Select **Add**

    ![Screenshot of the Add button.](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image40.png "Add button")

13. Select **Owner**

    ![In the Select a role blade, Owner is circled.](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image41.png "Select a role blade")

14. Select the **BU-Electronics-Users group** \> **Select** \> **OK** to add the group to the role 

    ![BU-Electronics-Users is circled.](images/Hands-onlabstep-by-step-Enterprise-readycloudimages/media/image42.png "BU-Electronics-Users ")

## After the hands-on lab 

Duration: 10 minutes

After completing the hands-on lab, you will remove the policies on your subscription.

### Task 1: Remove resources and configuration created during this lab

You should follow all steps provided *after* attending the hands-on lab.

1.  Log in to the Azure portal

2.  Navigate to the **Policy** blade

3.  Select **Assignments**, and delete all policy assignments created during this lab

4.  Select **Definitions**, and delete any policy definitions or initiative definitions created during this lab

5.  Navigate to the **Management Groups** blade

6.  Remove any Management Groups crated during this lab

7.  Navigate to the **Resource Groups** blade

8.  Remove any resource groups created during this lab. This will also delete any resources in those resource groups.

9.  Navigate to the **Azure Active Directory** blade

10. Remove any users and groups created during this lab

You should follow all steps provided *after* attending the Hands-on lab.
