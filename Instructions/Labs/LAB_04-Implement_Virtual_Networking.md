---
lab:
    title: '04 - Implement Virtual Networking'
    module: 'Administer Virtual Networking'
---

# Lab 04 - Implement Virtual Networking

# Student lab manual

## Lab scenario

You need to explore Azure virtual networking capabilities. To start, you plan to create a virtual network in Azure that will host a couple of Azure virtual machines. Since you intend to implement network-based segmentation, you will deploy them into different subnets of the virtual network. You also want to make sure that their private and public IP addresses will not change over time. To comply with Contoso security requirements, you need to protect public endpoints of Azure virtual machines accessible from Internet. Finally, you need to implement DNS name resolution for Azure virtual machines both within the virtual network and from Internet.

**Note:** An **[interactive lab simulation](https://mslabs.cloudguides.com/guides/AZ-104%20Exam%20Guide%20-%20Microsoft%20Azure%20Administrator%20Exercise%208)** is available that allows you to click through this lab at your own pace. You may find slight differences between the interactive simulation and the hosted lab, but the core concepts and ideas being demonstrated are the same. 

## Objectives

In this lab, you will:

+ Task 1: Create and configure a virtual network
+ Task 2: Deploy virtual machines into the virtual network
+ Task 3: Configure private and public IP addresses of Azure VMs
+ Task 4: Configure network security groups
+ Task 5: Configure Azure DNS for internal name resolution
+ Task 6: Configure Azure DNS for external name resolution

## Estimated timing: 40 minutes

## Architecture diagram

![image](../media/lab04.png)

## Instructions

### Exercise 1

#### Task 1: Create and configure a virtual network

In this task, you will create a virtual network with multiple subnets by using the Azure portal

1. Sign in to the [**Azure portal**](https://portal.azure.com) and login using the uc mail ID click profile and switch directory to CECH SoIT Bootcamp.

1. In the Azure portal, search for and select **Virtual networks**, and, on the **Virtual networks** blade, click **+ Create**.

1. Create a virtual network with the following settings (leave others with their default values):

    | Setting | Value |
    | --- | --- |
    | Subscription | the name of the Azure subscription you will be using in this lab |
    | Resource Group | Create New -> **<6+2>-az104-04-rg1** |
    | Name | **<6+2>-vnet1** |
    | Region | East US |

1. Click **Next : IP Addresses** and delete the existing **IPv4 address space**. 

1. Click Add an IP Address space**IPv4 address space** enter the following values then click **Add**.

    | Setting | Value |
    | --- | --- |
    | Address space type | **IPV4** |
    | Starting address | **10.40.0.0** |
    | Address space size | **20(4096 Addresses** |

1. Click **+ Add subnet** enter the following values then click **Add**.

    | Setting | Value |
    | --- | --- |
    | Subnet template | **Default** |
    | Subnet name | **subnet0** |
    | Starting Address | **10.40.0.0** |
    | Subnet size | **/24(256 Addresses)** |

1. Accept the defaults and click **Review and Create**. Let validation occur, and hit **Create** again to submit your deployment.

    >**Note:** Wait for the virtual network to be provisioned. This should take less than a minute.

1. Click on **Go to resource** wait until the deployement finishes.

    >**[Screenshot 1](https://github.com/venkatvvg/AZ-104-MicrosoftAzureAdministrator-master/blob/master/Instructions/Labs/LAB_04-Implement_Virtual_Networking.md)**: Showing the overview of <6+2>-vnet1.


1. On the **<6+2>-vnet1** virtual network blade, click **Subnets** and then click **+ Subnet**.

1. Create a subnet with the following settings (leave others with their default values):

    | Setting | Value |
    | --- | --- |
    | Name | **subnet1** |
    | Subnet Address range (CIDR block) | **10.40.1.0/24** |
    | NAT Gateway | **None** |
    | Network security group | **None** |
    | Route table | **None** |

1. Click **Save**

>**[Screenshot 2](https://github.com/venkatvvg/AZ-104-MicrosoftAzureAdministrator-master/blob/master/Instructions/Labs/LAB_04-Implement_Virtual_Networking.md)**: Displaying 2 subnets on the subnet overview page of <6+2>-vnet1.

#### Task 2: Deploy virtual machines into the virtual network

In this task, you will deploy Azure virtual machines into different subnets of the virtual network by using an ARM template

1. In the Azure portal, open the **Azure Cloud Shell** by clicking on the icon in the top right of the Azure Portal.

1. If prompted to select either **Bash** or **PowerShell**, select **PowerShell**.

1. From the Cloud Shell pane, click advanced settings using following:

   ```powershell
   Resource Group -> Use existing -> <6+2>-az-104-04-rg1   eg:madalrt-az-104-04-rg1 
   ```
   ```powershell
   Storage Account -> Create New -> <6+2>04storage eg:madalrt04storage 
   ```
   ```powershell
   File Share -> Create New -> <6+2>04fs  eg:madalrt04fs  
   ```
    >**Note**: If powershell open automatically click Settings --> Reset user settings**.


1. Download the two files **[\\Allfiles\\Labs\\04\\az104-04-vms-loop-template.json](https://drive.google.com/file/d/17TZ7cyZEM2CqTzoga82hkG6eNsyQ16jh/view?usp=sharing)** and **[\\Allfiles\\Labs\\04\\az104-04-vms-loop-parameters.json](https://drive.google.com/file/d/1yMd3CPpJat9vesxJycd40kviSX58NjfV/view?usp=share_link)**

1. Edit the Parameters file using notepad, change the password (your password should be 8-16 character length and should have uppercase character, lowercase character, numeric digit, special character and cannot have Control characters) Click **Save**.

1. Edit the templete file in the notepad and replace <6+2> with your 6+2 example: mandalrt, Click **Save**. 

1. In the toolbar of the Cloud Shell pane, click the **Upload/Download files** icon, in the drop-down menu, click **Upload**. Upload **

    >**Note**: You must upload each file separately. After uploading, use **dir** to ensure both files were successfully uploaded.

1. From the Cloud Shell pane, run the following to deploy two virtual machines by using the template and parameter files:

   ```powershell
   $rgName = '<6+2>-az104-04-rg1'
   ```
   ```powershell
   New-AzResourceGroupDeployment `
      -ResourceGroupName $rgName `
      -TemplateFile $HOME/az104-04-vms-loop-template.json `
      -TemplateParameterFile $HOME/az104-04-vms-loop-parameters.json
   ```
   
   >**[Screenshot 3](https://github.com/venkatvvg/AZ-104-MicrosoftAzureAdministrator-master/blob/master/Instructions/Labs/LAB_04-Implement_Virtual_Networking.md)**: Terminal Output of successful run of the template deployement.


    >**Note**: This method of deploying ARM templates uses Azure PowerShell. You can perform the same task by running the equivalent Azure CLI command **az deployment create** (for more information, refer to [Deploy resources with Resource Manager templates and Azure CLI](https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/deploy-cli).

    >**Note**: Wait for the deployment to complete before proceeding to the next task. This should take about 2 minutes.

    >**Note**: If you got an error stating the VM size is not available please ask your instructor for assistance and try these steps:
    > 1. Click on the `{}` button in your CloudShell, select the **az104-04-vms-loop-parameters.json** from the left hand side bar and take a note of the `vmSize` parameter value.
    > 1. Check the location in which the 'az104-04-rg1' resource group is deployed. You can run `az group show -n az104-04-rg1 --query location` in your CloudShell to get it.
    > 1. Run `az vm list-skus --location <Replace with your location> -o table --query "[? contains(name,'Standard_D2s')].name"` in your CloudShell. If there are no listed SKUs (i.e. there are no results), then you cannot deploy any D2S virtual machines in that region. You will need to find a region that will allow you to deploy D2S virtual machines. Once you have chosen a suitable location, delete the AZ104-04-rg1 resource group and restart the lab.
    > 1. Replace the value of `vmSize` parameter with one of the values returned by the command you just run.
    > 1. Now redeploy your templates by running the `New-AzResourceGroupDeployment` command again. You can press the up button a few times which would bring the last executed command.

1. Close the Cloud Shell pane.

#### Task 3: Configure private and public IP addresses of Azure VMs

In this task, you will configure static assignment of public and private IP addresses assigned to network interfaces of Azure virtual machines.

   >**Note**: Private and public IP addresses are actually assigned to the network interfaces, which, in turn are attached to Azure virtual machines, however, it is fairly common to refer to IP addresses assigned to Azure VMs instead.

1. In the Azure portal, search for and select **Resource groups**, and, on the **Resource groups** blade, click **<6+2>-az104-04-rg1**.

1. On the **<6+2>-az104-04-rg1** resource group blade, in the list of its resources, click **<6+2>-vnet1**.

1. On the **<6+2>-vnet1** virtual network blade, review the **Connected devices** section and verify that there are two network interfaces **<6+2>-nic0** and **<6+2>-nic1** attached to the virtual network.

1. Click **<6+2>-nic0** and, on the **<6+2>-nic0** blade, On settings Pane --> click **IP configurations**.

    >**Note**: Verify that **ipconfig1** is currently set up with a dynamic private IP address.

1. In the list IP configurations, click **ipconfig1**.

1. On the **ipconfig1** blade, in the **Public IP address settings** section, select **Associate**, click **+ Create new**, specify the following settings, and click **OK**:

    | Setting | Value |
    | --- | --- |
    | Public IP address | **Associate** |
    | Name | **<6+2>-pip0** |
    | SKU | **Standard** |

1. On the **ipconfig1** blade, set **Assignment** to **Static**, leave the default value of **IP address** set to **10.40.0.4**.

1. Click **Save** the changes. Make sure to wait for the save operation to complete before you proceed to the next step.

1. Navigate back to the **<6+2>-vnet1** blade

1. Click **<6+2>-nic1** and, on the **<6+2>-nic1** blade, On settings Pane --> click **IP configurations**.

    >**Note**: Verify that **ipconfig1** is currently set up with a dynamic private IP address.

1. In the list IP configurations, click **ipconfig1**.

1. On the **ipconfig1** blade, in the **Public IP address settings** section, select **Associate**, click **+ Create new**, specify the following settings, and click **OK**:

    | Setting | Value |
    | --- | --- |
    | Public IP address | **Associate** |
    | Name | **<6+2>-pip1** |
    | SKU | **Standard** |
    | Assignment | **Static** |

1. Leave the default value of **IP address** set to **10.40.1.4**.

1. Click **Save** the changes. Make sure to wait for the save operation to complete before you proceed to the next step.

   >**[Screenshot 4](https://github.com/venkatvvg/AZ-104-MicrosoftAzureAdministrator-master/blob/master/Instructions/Labs/LAB_04-Implement_Virtual_Networking.md)**: Display IP Configurations of <6+2>-nic1 

1. Navigate back to the **<6+2>-az104-04-rg1** resource group blade, in the list of its resources, click **<6+2>-vm0**, and from the **<6+2>-vm0** virtual machine blade, note the public IP address entry.

1. Navigate back to the **<6+2>-az104-04-rg1** resource group blade, in the list of its resources, click **<6+1>-vm1**, and from the **<6+2>-vm1** virtual machine blade, note the public IP address entry.

    >**Note**: You will need both IP addresses in the last task of this lab.

#### Task 4: Configure network security groups

In this task, you will configure network security groups in order to allow for restricted connectivity to Azure virtual machines.

1. In the Azure portal, navigate back to the **<6+2>-az104-04-rg1** resource group blade, and in the list of its resources, click **<6+2>-vm0**.

1. On the **<6+2>-vm0** overview blade, click **Connect**, click **RDP** in the drop-down menu, on the **Connect with RDP** blade, click **Download RDP File** using the Public IP address and follow the prompts to start the Remote Desktop session.

1. Stop the **<6+2>-vm0** and **<6+2>-vm1** virtual machines.

    >**Note**: This is done for lab expediency. If the virtual machines are running when a network security group is attached to their network interface, it can can take over 30 minutes for the attachment to take effect. Once the network security group has been created and attached, the virtual machines will be restarted, and the attachment will be in effect immediately.

1. In the Azure portal, search for and select **Network security groups**, and, on the **Network security groups** blade, click **+ Create**.

1. Create a network security group with the following settings (leave others with their default values):

    | Setting | Value |
    | --- | --- |
    | Subscription | the name of the Azure subscription you are using in this lab |
    | Resource Group | **<6+2>-az104-04-rg1** (Select from the Dropdown) |
    | Name | **<6+2>-nsg01** |
    | Region | East US |

1. Click **Review and Create**. Let validation occur, and hit **Create** to submit your deployment.

    >**Note**: Wait for the deployment to complete. This should take about 2 minutes.

1. On the deployment blade, click **Go to resource** to open the **<6+2>-nsg01** network security group blade.

   >**[Screenshot 5](https://github.com/venkatvvg/AZ-104-MicrosoftAzureAdministrator-master/blob/master/Instructions/Labs/LAB_04-Implement_Virtual_Networking.md)**: Show the overview of <6+2>-nsg01.

1. On the **<6+2>-nsg01** network security group blade, in the **Settings** section, click **Inbound security rules**.

1. Add an inbound rule with the following settings (leave others with their default values):

    | Setting | Value |
    | --- | --- |
    | Source | **Any** |
    | Source port ranges | * |
    | Destination | **Any** |
    | Service | **RDP** |
    | Action | **Allow** |
    | Priority | **300** |
    | Name | **AllowAnyRDPInbound-<6+2>** |
    
    >**[Screenshot 6](https://github.com/venkatvvg/AZ-104-MicrosoftAzureAdministrator-master/blob/master/Instructions/Labs/LAB_04-Implement_Virtual_Networking.md)**: Show the Inbound Security rules of <6+2>-nsg01

1. On the **<6+2>-nsg01** network security group blade, in the **Settings** section, click **Network interfaces** and then click **+ Associate**.

1. Associate the **<6+2>-nsg01** network security group with the **<6+2>-nic0** and **<6+2>-nic1** network interfaces.

    >**Note**: It may take up to 5 minutes for the rules from the newly created Network Security Group to be applied to the Network Interface Card.

1. Start the **<6+2>-vm0** and **<6+2>-vm1** virtual machines on the portal .

1. Navigate back to the **<6+2>-vm0** virtual machine blade.

    >**Note**: In the subsequent steps, you will verify that you can successfully connect to the target virtual machine.

1. On the **<6+2>-vm0** blade, click **Connect**, click **RDP**, on the **Connect with RDP** blade, click **Download RDP File** using the Public IP address and follow the prompts to start the Remote Desktop session.

    >**Note**: This step refers to connecting via Remote Desktop from a Windows computer. On a Mac, you can use Remote Desktop Client from the Mac App Store and on Linux computers you can use an open source RDP client software.
    
    >**Note**: If you see your name as login click use different account.
    >If the parameters file is not updated then default Login will be Student and Pa55w.rd1234
    >**Note**: You can ignore any warning prompts when connecting to the target virtual machines.

1. When prompted, sign in with the user and password in the parameters file.

    >**Note**: Leave the Remote Desktop session open. You will need it in the next task.

    >**[Screenshot 7](https://github.com/venkatvvg/AZ-104-MicrosoftAzureAdministrator-master/blob/master/Instructions/Labs/LAB_04-Implement_Virtual_Networking.md)**: Display the RDP Connected to windows server.

#### Task 5: Configure Azure DNS for internal name resolution

In this task, you will configure DNS name resolution within a virtual network by using Azure private DNS zones.

1. In the Azure portal, search for and select **Private DNS zones** and, on the **Private DNS zones** blade, click **+ Create**.

1. Create a private DNS zone with the following settings (leave others with their default values):

    | Setting | Value |
    | --- | --- |
    | Subscription | the name of the Azure subscription you are using in this lab |
    | Resource Group | **<6+2>-az104-04-rg1** |
    | Name | **<6+2>.com** |

1. Click **Review and Create**. Let validation occur, and hit **Create** again to submit your deployment.

    >**Note**: Wait for the private DNS zone to be created. This should take about 2 minutes.

1. Click **Go to resource** to open the **<6+2>.com** DNS private zone blade.

    >**[Screenshot 8](https://github.com/venkatvvg/AZ-104-MicrosoftAzureAdministrator-master/blob/master/Instructions/Labs/LAB_04-Implement_Virtual_Networking.md)**: Overview of <6+2>.com (private DNS Zone)

1. On the **<6+2>.com** private DNS zone blade, in the **Settings** section, click **Virtual network links**

1. Click **+ Add** to create a virtual network link with the following settings (leave others with their default values):

    | Setting | Value |
    | --- | --- |
    | Link name | **<6+2>-vnet1-link** |
    | Subscription | the name of the Azure subscription you are using in this lab |
    | Virtual network | **<6+2>-vnet1** |
    | Enable auto registration | enabled |

1. Click **OK**.

    >**Note:** Wait for the virtual network link to be created. This should take less than 1 minute.

1. On the **<6+2>.com** private DNS zone blade, in the sidebar, click **Overview**

1. Verify that the DNS records for **<6+2>-vm0** and **<6+2>-vm1** appear in the list of record sets as **Auto registered**.

    >**Note:** You might need to wait a few minutes and refresh the page if the record sets are not listed.
    
    >**[Screenshot 9](https://github.com/venkatvvg/AZ-104-MicrosoftAzureAdministrator-master/blob/master/Instructions/Labs/LAB_04-Implement_Virtual_Networking.md)**: Overview of <6+2>.com (private DNS Zone)


1. Switch to the Remote Desktop session to **<6+2>-vm0**, right-click the **Start** button and, in the right-click menu, click **Windows PowerShell (Admin)**.

1. In the Windows PowerShell console window, run the following to test internal name resolution in the newly created private DNS zone:

   ```powershell
   nslookup <6+2>-vm0.<6+2>.com
   ```
   ```powershell
   nslookup <6+2>-vm1.<6+2>.com
   ```
1. Verify that the output of the command includes the private IP address of **<6+2>-vm0** (**10.40.0.4**).

1. 1. Verify that the output of the command includes the private IP address of **<6+2>-vm1** (**10.40.1.4**).

    >**[Screenshot 10](https://github.com/venkatvvg/AZ-104-MicrosoftAzureAdministrator-master/blob/master/Instructions/Labs/LAB_04-Implement_Virtual_Networking.md)**: Show the terminal outputs of 2 commands in the powershell

#### Task 6: Configure Azure DNS for external name resolution

In this task, you will configure external DNS name resolution by using Azure public DNS zones.

1. In the Azure portal, search for and select **DNS zones** and, on the **DNS zones** blade, click **+ Create**.

1. Create a DNS zone with the following settings (leave others with their default values):

    | Setting | Value |
    | --- | --- |
    | Subscription | the name of the Azure subscription you are using in this lab |
    | Resource Group | **<6+2>-az104-04-rg1** (select from the drop down)|
    | Name | <6+2>.com|

1. Click **Review and Create**. Let validation occur, and hit **Create** again to submit your deployment.

    >**Note**: Wait for the DNS zone to be created. This should take about 2 minutes.

1. Click **Go to resource** to open the blade of the newly created DNS zone.

1. On the DNS zone blade, click **+ Record set**.

1. Add a record set with the following settings (leave others with their default values):

    | Setting | Value |
    | --- | --- |
    | Name | **<6+2>-vm0** |
    | Type | **A** |
    | Alias record set | **No** |
    | TTL | **1** |
    | TTL unit | **Hours** |
    | IP address | the public IP address of **<6+2>-vm0** which you identified in the third exercise of this lab |

1. Click **OK**

1. On the DNS zone blade, click **+ Record set**.

1. Add a record set with the following settings (leave others with their default values):

    | Setting | Value |
    | --- | --- |
    | Name | **<6+2>-vm1** |
    | Type | **A** |
    | Alias record set | **No** |
    | TTL | **1** |
    | TTL unit | **Hours** |
    | IP address | the public IP address of **<6+2>-vm1** which you identified in the third exercise of this lab |

1. Click **OK**

    >**[Screenshot 11](https://github.com/venkatvvg/AZ-104-MicrosoftAzureAdministrator-master/blob/master/Instructions/Labs/LAB_04-Implement_Virtual_Networking.md)**: overview of the <6+2.com> (Dns Zone)

1. On the DNS zone blade, note the name of the **Name server 1** entry.

1. In the Azure portal, open the **PowerShell** session in **Cloud Shell** by clicking on the icon in the top right of the Azure Portal.

1. From the Cloud Shell pane, run the following to test external name resolution of the **<6+2>-vm0** DNS record set in the the newly created DNS zone (replace the placeholder `[Name server 1]` with the name of **Name server 1** you noted earlier in this task and the `[domain name]` placeholder with the name of the DNS domain you created earlier in this task):

   ```powershell
   nslookup <6+2>-vm0.[domain name] [Name server 1]
   ```

1. Verify that the output of the command includes the public IP address of **<6+2>-vm0**.

1. From the Cloud Shell pane, run the following to test external name resolution of the **<6+2>vm1** DNS record set in the the newly created DNS zone (replace the placeholder `[Name server 1]` with the name of **Name server 1** you noted earlier in this task and the `[domain name]` placeholder with the name of the DNS domain you created earlier in this task):

   ```powershell
   nslookup <6+2>-vm1.[domain name] [Name server 1]
   ```

1. Verify that the output of the command includes the public IP address of **<6+2>-vm1**.

    >**[Screenshot 12](https://github.com/venkatvvg/AZ-104-MicrosoftAzureAdministrator-master/blob/master/Instructions/Labs/LAB_04-Implement_Virtual_Networking.md)**: Azure Cloud Shell Output which show both DNS connections of VM0 & VM1



#### Clean up resources

>**Note**: Remember to remove the resource group that you created.


1. In the Azure portal, open the **PowerShell** session within the **Cloud Shell** pane.

1. Delete all resource groups you created throughout the labs of this module by running the following command:

   ```powershell
   Get-AzResourceGroup -Name '<6+2>*' | Remove-AzResourceGroup
   ```

    >**Note**: The command executes and takes some time for the execution.
    >**[Screenshot 12](https://github.com/venkatvvg/AZ-104-MicrosoftAzureAdministrator-master/blob/master/Instructions/Labs/LAB_04-Implement_Virtual_Networking.md)**: Azure Cloud Shell Output True


#### Review

In this lab, you have:

+ Created and configured a virtual network
+ Deployed virtual machines into the virtual network
+ Configured private and public IP addresses of Azure VMs
+ Configured network security groups
+ Configured Azure DNS for internal name resolution
+ Configured Azure DNS for external name resolution
