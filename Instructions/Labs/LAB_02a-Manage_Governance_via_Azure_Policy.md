---
lab:
    title: '02a - Manage Governance via Azure Policy'
    module: 'Administer Governance and Compliance'
---

# Lab 02a - Manage Governance via Azure Policy
# Student lab manual

## Lab scenario

In order to improve management of Azure resources in Contoso, you have been tasked with implementing the following functionality:

- tagging resource groups that include only infrastructure resources (such as Cloud Shell storage accounts)

- ensuring that only properly tagged infrastructure resources can be added to infrastructure resource groups

- remediating any non-compliant resources

**Note:** An **[interactive lab simulation](https://mslabs.cloudguides.com/guides/AZ-104%20Exam%20Guide%20-%20Microsoft%20Azure%20Administrator%20Exercise%203)** is available that allows you to click through this lab at your own pace. You may find slight differences between the interactive simulation and the hosted lab, but the core concepts and ideas being demonstrated are the same. 

## Objectives

In this lab, we will:

+ Task 1: Create and assign tags via the Azure portal
+ Task 2: Enforce tagging via an Azure policy
+ Task 3: Apply tagging via an Azure policy

## Estimated timing: 30 minutes

## Architecture diagram

![image](../media/lab02b.png)

## Instructions

### Exercise 1

#### Task 1: Assign tags via the Azure portal

In this task, you will create and assign a tag to an Azure resource group via the Azure portal.

1. Sign in to the [Azure portal](https://portal.azure.com) and login using the uc mail ID click profile and switch directory to CECH SoIT Bootcamp.

1. In the Azure portal, start a **PowerShell** session by clicking on the **Cloud Shell** in header section beside the search box.

1. From the Cloud Shell pane, click advanced settings using following:

   ```powershell
   Resource Group -> Create New -> <6+2>rg   eg:madalrtrg 
   ```
   ```powershell
   Storage Account -> Create New -> <6+2>storage eg:madalrtstorage 
   ```
   ```powershell
   File Share -> Create New -> <6+2>fs  eg:madalrtfs  
   ```

1. In the Azure portal, search and select **Storage accounts** and, in the list of the storage accounts, click the entry representing the storage account you created in the previous step. (<6+2>storage) 
    >**[Screenshot 1](https://github.com/venkatvvg/AZ-104-MicrosoftAzureAdministrator-master/blob/master/Instructions/Labs/LAB_02a-Manage_Governance_via_Azure_Policy.md)**: Show the overview of **Storage account** you created.

1. On the storage account blade, click the link resource group name link.

    >**Note**: Note what resource group the storage account is in, you'll need it later in the lab.

1. On the resource group blade, click Click **edit** next to **Tags** to create new tags.

1. Create a tag with the following settings and Apply your change:

    | Setting | Value |
    | --- | --- |
    | Name | **UCID** |
    | Value | **<your 6+2>** Eg: mandalrt |

    >**[Screenshot 2](https://github.com/venkatvvg/AZ-104-MicrosoftAzureAdministrator-master/blob/master/Instructions/Labs/LAB_02a-Manage_Governance_via_Azure_Policy.md)**: Show the overview of your **Resource Group** with tag created.

1. Navigate back to the storage account blade. Review the **Overview** information and note that the new tag was not automatically assigned to the storage account. 

#### Task 2: Enforce tagging via an Azure policy

In this task, you will assign the built-in *Require a tag and its value on resources* policy to the resource group and evaluate the outcome. 

1. In the Azure portal, search for and select **Policy**. 

1. In the **Authoring** section, click **Definitions**. Take a moment to browse through the list of built-in policy definitions that are available for you to use. List all built-in policies that involve the use of tags by selecting the **Tags** entry (and de-selecting all other entries) in the **Category** drop-down list. 

1. Click the entry representing the **Require a tag and its value on resources** built-in policy and review its definition.

1. On the **Require a tag and its value on resources** built-in policy definition blade, click **Assign**.

1. Configure the **Basics** properties of the assignment by specifying the following settings (leave others with their defaults):

    | Setting | Value |
    | --- | --- |
    | Assignment name | **Require Role tag with UCID value <6+2>policy**|
    | Description | **Require Role tag with UCID value for all resources in the <6+2>rg resource group**|
    | Policy enforcement | Enabled |
    | Assigned by | <6+2> |

    >**Note**: The **Assignment name** is automatically populated with the policy name you selected, but you can change it. You can also add an optional **Description**. 

1. Specify the **Scope** by clicking the ellipsis button and selecting the following values:

    | Setting | Value |
    | --- | --- |
    | Subscription | the name of the Azure subscription you are using in this lab - CECH SoIT Bootcamp |
    | Resource Group | Your <6_2>rg resource group containing the Cloud Shell account you created in the previous task |

    >**Note**: A scope determines the resources or resource groups where the policy assignment takes effect. You could assign policies on the management group, subscription, or resource group level. You also have the option of specifying exclusions, such as individual subscriptions, resource groups, or resources (depending on the assignment scope).

1. Click **Next** twice and set **Parameters** to the following values:

    | Setting | Value |
    | --- | --- |
    | Tag Name | **UCID** |
    | Tag Value | **<6+2>** |

1. Click **Next** and review the **Remediation** tab. Leave the **Create a Managed Identity** checkbox unchecked. 

    >**Note**: This setting can be used when the policy or initiative includes the **deployIfNotExists** or **Modify** effect.

1. Click **Review + Create** and then click **Create**.

    >**Note**: Now you will verify that the new policy assignment is in effect by attempting to create another Azure Storage account in the resource group without explicitly adding the required tag. 
    
    >**Note**: It might take between 5 and 15 minutes for the policy to take effect.

1. Navigate to **Policy**, under Authoring navigate to Assignments Blade 

    >**[Screenshot 3](https://github.com/venkatvvg/AZ-104-MicrosoftAzureAdministrator-master/blob/master/Instructions/Labs/LAB_02a-Manage_Governance_via_Azure_Policy.md)**: Show the **Assignment Name** you created under the Assignment Blade.

1. Navigate back to the blade of the resource group <6+2>rg hosting the storage account, which you created in the previous task.

1. On the resource group blade, click **+ Create** and then search for **Storage Account**, and click **+ Create**. 

1. On the **Basics** tab of the **Create storage account** blade, verify that you are using the Resource Group that the Policy was applied to and specify the following settings (leave others with their defaults), click **Review** and then click **Create**:

    | Setting | Value |
    | --- | --- |
    | Resource Group | your <6+2>rg |
    | Storage account name | <6+2>storage1 eg:mandalrtstorage1 |
    | Region | Default (US) East US|
    | Perforomace | **Standard** |
    | Redundacy | Local Redudant Storage (LRS) | 

    >**Note**: You may receive a **Validation failed. Click here for details** error; If so, click the error message to identify the reason for the failure and skip the next step. 

1. click **Review** and then click **Create**

1. Once you create the deployment, you should see the **Deployment failed** message in the **Notifications** list of the portal. From the **Notifications** list, navigate to the deployment overview and click the **Deployment failed. Click here for details** message to identify the reason for the failure. 

    >**Note**: Verify whether the error message states that the resource deployment was disallowed by the policy. 

    >**Note**: By clicking the **Raw Error** tab, you can find more details about the error, including the name of the role definition **Require Role tag with UCID value**. The deployment failed because the storage account you attempted to create did not have a tag named **UCID** with its value set to **<6+2>**.

    >**[Screenshot 4](https://github.com/venkatvvg/AZ-104-MicrosoftAzureAdministrator-master/blob/master/Instructions/Labs/LAB_02a-Manage_Governance_via_Azure_Policy.md)**: Show the error you received during deployment.

#### Task 3: Apply tagging via an Azure policy

In this task, we will use a different policy definition to remediate any non-compliant resources. 

1. In the Azure portal, search for and select **Policy**. 

1. In the **Authoring** section, click **Assignments**. 

1. Click **Assign policy** and specify the **Scope** by clicking the ellipsis button and selecting the following values:

    | Setting | Value |
    | --- | --- |
    | Subscription | the name of the Azure subscription you are using in this lab |
    | Resource Group | <6+2> Resource group you created intially |

1. To specify the **Policy definition**, click the ellipsis button and then search for and select **Inherit a tag from the resource group if missing**.

1. Configure the remaining **Basics** properties of the assignment by specifying the following settings (leave others with their defaults):

    | Setting | Value |
    | --- | --- |
    | Assignment name | **Inherit the UCID tag and its <6+2> value from the resource group if missing**|
    | Description | **Inherit the UCID tag and its <6+2> value from the resource group if missing**|
    | Policy enforcement | Enabled |
    | Assigned by | <6+2> |

1. Click **Next** twice and set **Parameters** to the following values:

    | Setting | Value |
    | --- | --- |
    | Tag Name | **UCID** |

1. Click **Next** and, on the **Remediation** tab, configure the following settings (leave others with their defaults):

    | Setting | Value |
    | --- | --- |
    | Create a remediation task | enabled |
    | Policy to remediate | **Inherit a tag from the resource group if missing** |

    >**Note**: This policy definition includes the **Modify** effect.

1. Click **Review + Create** and then click **Create**.

    >**Note**: To verify that the new policy assignment is in effect, you will create another Azure Storage account in the same resource group without explicitly adding the required tag. 
    
    >**Note**: It might take between 5 and 15 minutes for the policy to take effect.

1. Navigate to **Policy**, under Authoring navigate to Assignments Blade 

    >**[Screenshot 5](https://github.com/venkatvvg/AZ-104-MicrosoftAzureAdministrator-master/blob/master/Instructions/Labs/LAB_02a-Manage_Governance_via_Azure_Policy.md)**: Show the **Assignment Name** you created under the Assignment Blade.

1. Navigate back to the blade of the resource group hosting the storage account used for the Cloud Shell home drive, which you identified in the first task.

1. On the resource group blade, click **+ Create** and then search for **Storage Account**, and click **+ Create**. 

1. On the **Basics** tab of the **Create storage account** blade, verify that you are using the Resource Group that the Policy was applied to and specify the following settings (leave others with their defaults) and click **Review**:

    | Setting | Value |
    | --- | --- |
    | Resource Group | your <6+2>rg |
    | Storage account name | <6+2>storage1 eg:mandalrtstorage1 |
    | Region | Default (US) East US|
    | Perfromace | **Standard** |
    | Redundacy | Local Redudant Storage (LRS) | 

1. Verify that this time the validation passed and click **Create**.

1. Once the new storage account is provisioned, click **Go to resource** button and, on the **Overview** blade of the newly created storage account, note that the tag **UCID** with the value **<6+2>** has been automatically assigned to the resource.

    >**Note**: If you have observed in Task 2, you enforced taging and when you tried creating a tag policy stopped you from creating the resource since you didn't specify the Tag. In Task 3 you applied tagging via policy, now when you create the resouce again without the tag you were able to sucessfully deploy it as the tag was auto assigned.

    >**[Screenshot 6](https://github.com/venkatvvg/AZ-104-MicrosoftAzureAdministrator-master/blob/master/Instructions/Labs/LAB_02a-Manage_Governance_via_Azure_Policy.md)**: Show the overview of **Storage account** you created with the tag added.

#### Review

In this lab, you have:

- Created and assigned tags via the Azure portal
- Enforced tagging via an Azure policy
- Applied tagging via an Azure policy
