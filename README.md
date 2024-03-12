![activedirect](https://github.com/DashonJennings/DeployingActiveDirectoryInAzure/assets/160358839/87ea9c0f-f0a6-4c57-9d0e-d582350978e9)

# Deploying Active Directory in Azure

### Summary
Ative Directory (AD) is Microsoft's service to manage Window's domain networks. It is what most enterprises build their IT departments on.
The Active Directory database contains critical information about the environment and the services control much of the activity that goes on within the IT environment.
It connects users to the network resources they need by implementing organization and security measures. It authenticates users with the user ID and password,
and authorizes them to access only the data they are allowed to by using permission rules.
This is how the same set of credentials can be used to log into any Windows workstation within a given institution. 
This lab's purpose is to understand how Active Directory and Windows networking work by setting up a virtual environment with 1,000+ users.

### Learning Objectives:
- How to provision multiple virtual machines on VirtualBox
- Install and configure Windows Server 2019
- Create a Windows 10 ISO with Microsoft's media creation tool
- Install and configure Windows 10
- Install and configure Microsoft's Active Directory Domain Services (AD DS)
- Create 1,000+ users with Windows PowerShell
- Enterprise level user management

### Tools and Requirements:
1. Oracle VirtualBox
2. Windows Server 2019 ISO
3. Windows 10 ISO
4. Directory service: Active Directory Domain Services (AD DS)
5. PowerShell


### Overview:
![win_active_dir](https://github.com/DashonJennings/DeployingActiveDirectoryInAzure/assets/160358839/3d410c34-1ed9-48b8-b3e0-ca030b766830)


## Step 1: Download and Install [Oracle VirtualBox](https://www.virtualbox.org/wiki/Downloads)
- Select the appropriate download based on the current host OS
- Install VirtualBox with the Wizard defaults or desired preferences
- Download and install the VM VirtualBox Extension Pack
> NOTE: This setup will use a Windows 11 host OS.

![virtualbox_download](https://github.com/DashonJennings/DeployingActiveDirectoryInAzure/assets/160358839/0c6ec24e-3f0e-4eeb-8264-f1855c98074a)


## Step 2: Download [Windows Server 2019 ISO](https://www.microsoft.com/en-us/evalcenter/download-windows-server-2019)
- English (United States)
- ISO File 
- 64-bit edition

![win_server_download](https://github.com/DashonJennings/DeployingActiveDirectoryInAzure/assets/160358839/49a838ec-a361-4e93-aa5e-fdcb5de9db38)


## Step 3: Download Microsoft's [Media Creation Tool](https://www.microsoft.com/en-us/software-download/windows10) and Windows 10 ISO
> NOTE: In order to download the Windows 10 ISO the media creation tool is required.

### Create Windows 10 Installation Media 
- Select **Download Now**
- Install with defualt options
- Follow the the prompts to download the Windows 10 ISO file.

![create_win_inst](https://github.com/DashonJennings/DeployingActiveDirectoryInAzure/assets/160358839/5bb3c836-7282-4152-9611-1dead2c354af)


#### What do you want to do?
- **Create installation media (USB flas drive, DVD, or ISO file) for another PC**

![create_media](https://github.com/DashonJennings/DeployingActiveDirectoryInAzure/assets/160358839/6d6c8952-2b07-4743-a58f-3daaa34040af)


#### Select language, architecture and edition
- **Language:** English (United States)
- **Edition:** Windows 10
- **Architecture:** 64-bit (x64)
> NOTE: Keep recommended options, otherwise uncheck button to edit selections.

![create_media_2](https://github.com/DashonJennings/DeployingActiveDirectoryInAzure/assets/160358839/4dd7948f-4284-48ca-a5b0-54fb5ba4b745)


#### Choose which media to use
- **ISO file**
- Click **Finish** to exit the program

![create_media_3](https://github.com/DashonJennings/DeployingActiveDirectoryInAzure/assets/160358839/98eced46-d905-40c9-aeea-59ab75728745)


## Step 4: Create and Configure Domain Controller Virtual Machine
#### Virtual Machine Name and Operating System
- Open VirtualBox and select **Machine** to bring a drop down menu and select **New**
- **Name:** Domain Controller
- **Type:** Microsoft Windows
- **Version:** Other Windows (64-bit)

![new_vm](https://github.com/DashonJennings/DeployingActiveDirectoryInAzure/assets/160358839/f555cd73-dc20-4059-81cb-885673d9947a)


#### Hardware
- **Base Memory:** 2048 MB (2GB)
- **Processors:** 2-4 CPUs

#### Virtual Hard Disk
- **Disk Size:** 50.00GB

#### Settings
- **General** > **Advanced** > **Shared Clipboard** > **Bidirectional**
- **General** > **Advanced** > **Drag'n'Drop** > **Bidirectional**
> NOTE: These options will enable copying and pasting, as well as dragging and dropping between host and VM
- **Network** > **Adapter 1** > **Attatched to:** NAT
- **Network** > **Adapter 2** > Check **Enable Network Adapter** Box > **Attatched to:** Internal Network

![dc_properties](https://github.com/DashonJennings/DeployingActiveDirectoryInAzure/assets/160358839/d299453b-b2a3-49fa-998d-d07aa8a8b409)



## Step 5: Install Windows Server 2019 and Change PC Name
### Install Windows Server 2019
- Double click the **Domain Controller** VM
- It will fail to boot because it is missing the OS
- Select the DVD dropdown menu and find the Windows Server 2019 ISO file
- Click **Mount and Retry Boot**

![mount_iso](https://github.com/DashonJennings/DeployingActiveDirectoryInAzure/assets/160358839/dafc1230-0021-440f-a9bc-abca84c75b2e)


- Select Windows Server 2019 Standard Evaluation (Desktop Experience)
- Select **Custom: Insall Windows only (advanced) and click **Next**
- Configure Administrator account:
    - **Username** Administrator
    - **Password** Password1
- To press **CTRL+ALT+DEL** Select **Input** > **Keyboard** > **Insert CTRL+ALT+DEL**

![unlock](https://github.com/DashonJennings/DeployingActiveDirectoryInAzure/assets/160358839/dc7a7ffd-a277-4ad7-ad3a-e1aefb9400c7)


- Login and close out of notifications and pop ups
- On VM menu select **Devices** > **Insert Guest Additions CD Image**
- Open **File Explorer** > **This PC** > **CD Drive D: VirtualBox Guest Additions** > **VBoxWindowsAdditions-amd64**
- Select **I want to manually reboot later** and click **Finish**
### Change PC Name
- Right-click **Windows start icon** > **System** > **Rename this PC**
- Name it "DC" > **Restart later** 
- Manually shutdown the VM and restart

![guest_additions](https://github.com/DashonJennings/DeployingActiveDirectoryInAzure/assets/160358839/c13739da-b1d1-4542-886b-2b8ef1869984)


## Step 6: Configure Internal and External NICs
- Select the network icon on the bottom right of the screen and click **Network** > **Change adapter options**
- Right-click **Unidentified Network** > **Status** > **Details**
- It will not have internet access because the adapter is looking for a DHCP server that has not been configured yet

![no_dhcp](https://github.com/DashonJennings/DeployingActiveDirectoryInAzure/assets/160358839/6d06418f-fc2d-4a08-bb62-d1176939ccb5)


- Rename adapters accordingly:
  - Ethernet 1: INTERNETexternal
  - Ethernet 2: INTRANETinternal
  
![nic_names](https://github.com/DashonJennings/DeployingActiveDirectoryInAzure/assets/160358839/fbfd1dda-e448-4af3-94f6-c4fcac5a68ff)


- Right-click the internal network adapter and select **properties**
- **Internet Protocol Version 4 (TCP/IPv4)**
  - **IP:** 172.16.0.1
  - **Subnet Mask:** 255.255.255.0
  - **Default Gateway:** <Empty> 
  > NOTE: The Domain Controller will serve as the Default Gateway
- Installing Active Directory automatically installs DNS therefore the server will use itself as the DNS server
  - **Preferred DNS Server:** 127.0.0.1 (Loopback address)

![ip_configs](https://github.com/DashonJennings/DeployingActiveDirectoryInAzure/assets/160358839/17345532-8416-4f60-9e0e-27dfc058900a)


## Step 7: Install Active Directory Domain Services (AD DS) and Create a Domain
### Install AD DS
- **Server Manager** > **Add roles and features**
- Under **Server Roles** check the **Active Directory Domain Services** box > **Add features**
- Continue to click **Next** with the default options and **Install**
- Within the Server Manager Dashboard click the **Notifications Flag Icon** > **Promote this server to a domain controller**

![adds_inst_progress](https://github.com/DashonJennings/DeployingActiveDirectoryInAzure/assets/160358839/fe5adc04-e788-475a-9250-97f81ee501b7)


### Create a Domain
- Under **Deployment Configuration** > **Add a new forest**
  - **Root domain name:** "mydomain.com"
- Under **Domain Controller Options** > **type the Directory Services Restore Mode (DSRM) password** you would put the restoration password
  - **Password:** Password1
- Continue to click **Next** with all the default fields and **Install**

### Create a Dedicated Admin User Account
- **Start** > **Windows Administrative Tools** > **Active Directory Users and Computers** 
- Right-click **mydomain.com** > **New** > **Organizational Unit**
  - **Name:** "_ADMINS"
> NOTE: This will not give it the admin permissions yet. That will be configured later

![organizational_unit](https://github.com/DashonJennings/DeployingActiveDirectoryInAzure/assets/160358839/31399152-5f5e-46bb-81ca-f10374b0a36f)


- Right-click the **_ADMINS** Organizational Unit > **New** > **User**
- Fill out first and last name
  - E.g. John Doe's administrator account: a-jdoe 
  - **Password:** "Password1" 
  - Uncheck **User must change password at next login** 
  - Check **Password never expires**
> NOTE: The "a-" is to signify that it is an administrator's account
- Right-click new user > **Properties** > **Member of** > **Add** 
- Enter "domain admins" into field and press **Check Names** to resolve itself
- Press **Ok** > **Apply** > **Ok**
- Sign out of the Domain Controller and login with new credentials

![add_to_admin](https://github.com/DashonJennings/DeployingActiveDirectoryInAzure/assets/160358839/49c3ce2d-f53c-40a0-be3c-e1b5a5d0c148)


## Step 8: Install Remote Access Server (RAS) and Network Address Translation (NAT)
### Install RAS
> NOTE: This will allow the clients in the domain access to the internet through the Domain Controller.
- In the Server Manager Dashboard select **Add roles and features** 
- Under **Server Roles** check the **Remote access** box
- Under **Role Services** check the **Routing** box and click **Add features**
- Click **Next** with defaults and **Install** 
### Install NAT
- **Tools** > **Routing and Remote Access**
- Right-click **DC Local** > **Configure and Enable Routing and Remote Access**
- Select **Network Address Translaion (NAT)**
- Select the "INTERNETexternal" NIC
> NOTE: If the option is greyed out, cancel the operation and try again

![server_ras_nat](https://github.com/DashonJennings/DeployingActiveDirectoryInAzure/assets/160358839/43c4f1f6-d233-4268-ace6-a24100bc0e3a)


## Step 9: Set up a Dynamic Host Configuration Protocol (DHCP) Server
> NOTE: This will allow Windows 10 clients to obtain an IP address and browse the internet 
- In the **Server Manager** Dashboard select **Add roles and features**
- Under **Server Roles** check the **DHCP Server** box > **Add features** 
- Click **Next** with defaults and **Install**
- Go to **Tools** > **DHCP**
- Right-click **IPv4** > **New scope**
- Name the scope with the IP address range (172.16.0.100-200)
  - **Scope Name:** "172.16.0.100-200"
  - **Start IP Address:** 172.16.0.100
  - **End IP Address:** 172.16.0.200
  - **Length:** 24 (/24 CIDR)
  - **Subnet Mask** 255.255.255.0
- Set the default gateway to the Domain Controller's IP address and click **Add**
  - **Router (Default Gatweway):** "172.16.0.1"
- Click **Next** with all other defaults and **Finish** 
> NOTE: IPv4 DHCP might still be red and down so right-click **dc.mydomain.com** > **Authorize** and **Refresh** 

![dhcp](https://github.com/DashonJennings/DeployingActiveDirectoryInAzure/assets/160358839/500ae0fd-7635-4170-8e53-84ae7f750f22)


## Step 10: Use PowerShell Script to add 1,000+ Users
### Enable Web Browsing on Domain Controller
> NOTE: This will be done for the lab ONLY. It should never be replicated in an actual production environment for security reasons.
- **Configure this local server** > Disable **IE Enhanced Security Configuration**
- Select **OFF** for **Administrators** and **Users** 
### Download the PowerShell Script
- Open Internet Explorer to download the [PowerShell Script](https://github.com/joshmadakor1/AD_PS) to the Desktop (Written by Josh Madakor)
- **Code** > **Download Zip**

![script_download](https://github.com/DashonJennings/DeployingActiveDirectoryInAzure/assets/160358839/5295d3a4-4984-446f-b65f-023b0b82aae3)


- Open the "names.txt" file and add your name to the top of the list and save
- **Start** > right-click **Windows PowerShell ISE** > **More** > **Run as administrator** 
- Click the **Open Script** > "1_CREATE_USERS.ps1"
- Enable the Execution of all Scripts on Server
- In the PowerShell command line type: `Set-ExecutionPolicy Unrestricted` and hit **Enter** > **Yes to All** 

![execpolicy](https://github.com/DashonJennings/DeployingActiveDirectoryInAzure/assets/160358839/7dc122e1-38ca-455e-9f64-8cef132a69ec)


> NOTE: In essence this script will take the names from "names.txt" and save them into an array ($USER_FIRST_LAST_LIST = Get-Content .\names.txt)
The script will loop for each user and separate the first name from the last name. 
It will take the first name's first letter and join it to the last name to create a username and it will give them all the same password 
($PASSWORD_FOR_USERS   = "Password1").
Finally it will go through the process of adding each user to the domain (New-AdUser).

### Execute the Script
- Change directory in PowerShell's command line: `cd c:\Users\<username>\Desktop\AD-PS-master` and hit **Enter**
- Click the green **Play** button > **Run once**

![running_script](https://github.com/DashonJennings/DeployingActiveDirectoryInAzure/assets/160358839/ef52c913-acfa-4771-8171-c224e446fc1c)


- Go to **Server Manager** > **Active Directory Users and Computers** > right-click **mydomain.com** > **Refresh**
- It should now show the **_USERS** Organizational Unit with all the users created from the script
- To search for a specific user right-click **_USERS** > **Find** 

![users_ad](https://github.com/DashonJennings/DeployingActiveDirectoryInAzure/assets/160358839/76cbfb8c-c1b9-4cba-81e6-c1a0349accd2)

> NOTE: The users from the PowerShell Script now appear in AD.

![find_user](https://github.com/DashonJennings/DeployingActiveDirectoryInAzure/assets/160358839/59c817ed-f90c-4d61-8aff-e5cc1103eb60)

> NOTE: User Jim Halpert has been imported from the names.txt file and added to AD with the help of PowerShell

## Step 11: Create and Configure Windows 10 Client VM
#### Virtual Machine Name and Operating System
- Add a new VM
- **Name:** CLIENT1
- **Type:** Microsoft Windows
- **Version:** Windows 10 (64-bit)

#### Hardware
- **Base Memory:** 2048 MB (2GB)
- **Processors:** 2-4 CPUs

#### Virtual Hard Disk
- **Disk Size:** 50.00GB

#### Settings
- **General** > **Advanced** > **Shared Clipboard** > **Bidirectional**
- **General** > **Advanced** > **Drag'n'Drop** > **Bidirectional**
- **Network** > **Adapter 1** > **Attatched to:** Internal Network

## Step 12: Install and Configure Windows 10
### Install Windows 10 ISO
- Double click the **CLIENT1** VM
- Select the DVD dropdown menu and find the Windows 10 ISO file
- Click **Mount and Retry Boot**

![inst_win_pro](https://github.com/DashonJennings/DeployingActiveDirectoryInAzure/assets/160358839/924520f5-b871-46d7-af6c-4b340f16b927)


- **I don't have a product key**
- **Windows 10 Pro**
- **Custom: Insall Windows only (advanced)** and click **Next**
- **I don't have internet**
- **Set up for an organization**
- **Domain join instead**
- Add user account information
  - **Name:** user
  - **Password:** No password
- Disable all tracking privacy settings
- Skip customization services and Cortana

## Step 13: Verify Internet Connectivity

![client1_connection](https://github.com/DashonJennings/DeployingActiveDirectoryInAzure/assets/160358839/ef43ffdb-917b-4df4-b3ac-2c3d188bc32f)


- **Start** > "cmd"
- Type `ipconfig`
- Ping a website like Google: `ping www.google.com`

![server_dhcp_lease](https://github.com/DashonJennings/DeployingActiveDirectoryInAzure/assets/160358839/109fcad7-2a41-4ec1-a1d9-bf46f17e2e43)

> NOTE: DHCP is working on Domain Controller. CLIENT1 has an IP address leased to it and the default gateway is set to the DC's IP address.

## Step 14: Rename PC and Add to Domain
- **Start** > **Settings** > **System** > **About** > **Rename this PC (advanced)**
- Click **Change** and name "CLIENT1"
- Click **Member of Domain:** and add "mydomain.com" > **Ok** 
- Sign in with admin account to give CLIENT1 permissions to join domain
- **Close** and **Restart now**

![rename_client1](https://github.com/DashonJennings/DeployingActiveDirectoryInAzure/assets/160358839/606221a6-edec-4be6-bbac-a777f51e9703)


## Step 14: Login With Generated User Credentials

![new_user_login](https://github.com/DashonJennings/DeployingActiveDirectoryInAzure/assets/160358839/cf5bd94b-f805-41d1-83fa-597f15a77957)


- Select **Other user** on CLIENT1
- Use any of the usernames and password that were created with the PowerShell script to login
- **Start** > "cmd"
- Type `whoami`

![](images/p_beesly.png)
