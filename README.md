<h1> Implementing Active Directory (On-Premises) in Azure </h1> 

<img src="https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/5b730490-62e5-4e6a-99ed-903590512f80" width="600" />

<h2> What is Active Directory (AD)? </h2>
Active Directory (AD) is a Microsoft service for managing users, computers, and resources in a network. It centralizes security and administrative tasks, provides authentication and authorization, and organizes data into domains and organizational units. AD ensures data consistency and easy resource management across the network.
<br>
<br/>


In this tutorial, we'll set up our Domain Controller VM (DC-1) and a Client VM (C-1), ensure connectivity between C-1 and DC-1 install Active Directory on DC-1, create Admin and normal user accounts in AD, join C-1 to our domain, setup remote desktop for non-administrative users on C-1, create additional users on DC-1, and attempt to log into C-1 with one of those user accounts.

<h2> Environments and Technologies Used: </h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Windows Firewall
- Active Directory Domain Services (AD DS)
- Active Directory Users and Computers (ADUC)
- PowerShell

<h2> Operating Systems Used: </h2>

-  Windows 10
-  Windows Server 2022

<h2> List of Prerequisites: </h2>

-  Azure Account/Subscription

<h2> High-Level Steps: </h2>

1. Create our VMs
  - Create Domain Controller VM:
    - Deploy a Windows Server 2022 VM named "DC-1".
    - Set the NIC Private IP address to static.
  - Create Client VM:
    - Deploy a Windows 10 VM named "Client-1".
    - Use the same Resource Group and VNet as "DC-1".
    - Verify both VMs are in the same VNet.
  
2. Ensure Connectivity between Client and Domain Controller
  - Test Connectivity:
    - Log into "Client-1" via Remote Desktop.
    - Ping the private IP address of "DC-1".
    - On "DC-1", enable ICMPv4 in Windows Firewall.
    - Confirm successful ping from "Client-1".

3. Install Active Directory
  - Install AD DS:
    - On "DC-1", install Active Directory Domain Services.
    - Promote "DC-1" to a Domain Controller, setting up a new forest (e.g., mydomain.com).
    - Restart "DC-1" and log in as mydomain.com\labuser.

4. Create Admin and Normal User Accounts in AD
  - Create User Accounts:
    - In ADUC, create Organizational Units (OUs) "_EMPLOYEES" and "_ADMINS".
    - Create a new user "jane_admin" and add to the "Domain Admins" group.
    - Log in as mydomain.com\jane_admin.

5. Join Client-1 to the Domain
  - Configure DNS and Join Domain:
    - Set "Client-1" DNS to "DC-1"’s Private IP in Azure Portal.
    - Restart "Client-1".
    - Join "Client-1" to the domain and restart.
    - Verify "Client-1" in ADUC.

6. Setup Remote Desktop for Non-Admin Users on Client-1
  - Configure Remote Desktop:
    - Log into "Client-1" as mydomain.com\jane_admin.
    - Allow "domain users" access to Remote Desktop.

7. Create Additional Users and Test Logins
  - Automate User Creation:
    - On "DC-1", use PowerShell to run a script that creates multiple users.
    - Test logging into "Client-1" with one of the new accounts.

<h2> Steps: </h2> 

1. Create our VMs
  - Create Domain Controller VM:
    - Deploy a Windows Server 2022 VM named "DC-1".
      - We'll use "labuser" as our Username and "Password1234" as our password. We can move straight to "Review + Create". It should pass validation, and we can create our DC-1 VM.

<img src="https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/30f052a1-dac2-481b-95ab-940e337b87ec" width="600" /> 
<img src="https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/bcc45935-53d9-4f00-a8c9-9f98a413e981" width="600" />
<img src="https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/248024d9-5b1f-4760-8087-f7b2ab9725cd" width="600" />
<br>
<br/>

- Set the NIC Private IP address to static.
  - Within DC-1 we'll navigate to "Network Settings", "Network interface: dc-1755_z1", "IP configurations", "ipconfig", finally select "Static" and "Save".

<img src="https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/d555ae44-1f52-41bd-99b5-c5ab2444162c" width="600" />
<img src="https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/44679a7f-792f-4997-ae0d-256a014a65a0" width="600" />
<img src="https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/d7a36717-6276-4a59-999f-f781da369ff2" width="600" />
<img src="https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/0b65d1bf-9800-48fb-a6a7-647a3b7f2d77" width="600" />
<br>
<br/>
<img src="https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/6398d1bc-21e7-4015-bb26-ab87b13d74da" width="400" />
<br>
<br/>

- Create Client VM:
  - Deploy a Windows 10 VM named "Client-1".
    - We'll do the same exact steps we used to create DC-1 for Client-1. Expect we'll use Windows 10 for the operating system and DC-1's Vnet.
<img src="https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/1e27fc8f-6863-45c1-992e-e1afe8e27cdb" width="600" />
<img src="https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/c7ce825b-8050-4ada-90bd-4aef5c7f10ce" width="600" />
<img src="https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/0b735dca-0f35-4902-9460-60f88011d3c8" width="600" />
<br>
<br/>
   
- Verify both VMs are in the same VNet.
![Capture](https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/f95d5819-1fd2-4fcd-a4c8-86acb10482dc)
![Capture](https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/952949e2-59d1-40d6-b753-3345dcfb2bb9)
<br>
<br/>

2. Ensure Connectivity between Client and Domain Controller
  - Test Connectivity:
    - Log into "Client-1" via Remote Desktop.
      - We'll retrieve the Public IP address for Client-1 from the Azure Portal. Client-1's IP is 51.8.81.17. We'll use our "labuser" Username and "Password1234" for our Password.
![Capture](https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/d82e5cda-d587-4a75-9049-05e005bcb568)
![image](https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/31aa4e1d-3620-4a74-8001-49754a449ec0)
![image](https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/2172853f-4e87-40c1-95bc-3e2ed95ca514)
<br>
<br/>

- Ping the private IP address of "DC-1".
  - Once we log into Client-1, we'll ping the private address of DC-1 using the command "ping -t (IP address). We'll need the private IP address of DC-1. DC-1's Private IP address is 10.0.0.4. We'll notice that our ping request is consistently timing out. That's because DC-1's Firewall is currently blocking ICMPv4 traffic.
![Capture](https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/21af4e2b-d6f2-4bc1-a361-140e5d45d3ad)
![image](https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/afc256fe-34cd-4432-bec3-1b55d246fadb)
<img src="https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/2ef0804c-cc2f-4031-9b74-faa292dea319" width="600" />
<br>
<br/>

- We'll log into "DC-1" via Remote Desktop. We'll retrieve the Public IP address for DC-1 from the Azure Portal. DC-1's Public IP is 172.190.86.220. We'll use our "labuser" Username and "Password1234" for our Password.
![Capture](https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/3f0877bd-710b-473b-81cd-f82a5aee3290)
![image](https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/bc5b6ce6-16b5-44ff-9695-c30aefe6bcdc)
![image](https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/b0e5c3a4-7c5b-4454-85ac-6cc7432c0ebc)
<br>
<br/>
    
- On "DC-1", enable ICMPv4 in Windows Firewall.
  - We'll type "wf.msc" in the bottom search bar. Select "Inbound Rules", Sort by "Protocol" and Enable both "Core Networking Diagnostics - ICMP Echo Request (ICMPv4-IN)".
<img src="https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/79be48d0-234b-405c-a32c-afae578d47bc" width="500" />

![Capture](https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/c8533eb9-b6cf-443b-936d-d5e101db0323)
![Capture](https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/bcb2a80a-7a18-425b-9718-12aa3a1ba57e)
<br>
<br/>

- Confirm successful ping from "Client-1".
  - We should now see successful ping replies from Client-1 to DC-1 after Enabling ICMPv4 rules within DC-1's Firewall
![image](https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/f0719763-2518-4908-be59-91b335f60115)
<br>
<br/>

3. Install Active Directory
  - Install AD DS:
    - On "DC-1", install Active Directory Domain Services.
      - On DC-1, within the Server Manager Dashboard, we'll select "Add Roles and Features" and go through the setup with the preselected configurations until we get to the "Server Roles" section. Once there we'll select "Active Directory Domain Services", we'll be prompted with a new window, and we'll select "Add Features". We'll now select "Next" on all the pages until we get to the "Install" button, and once there, we'll install AD DS.
<img src="https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/2fd73930-32b1-499f-a419-556eda647ecc" width="500" />
<img src="https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/0327db5c-ee52-4e0a-ba74-bd5ef39aee9c" width="500" />
<img src="https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/d0b54991-0aa6-4378-8374-bd0f232a5c92" width="500" />
<img src="https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/ba729f41-fda7-49b1-a3c2-e8ef5f979177" width="500" />
<img src="https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/d3359b55-7281-4fdb-b6b4-4c701231dc43" width="500" />
<br>
<br/>

- Promote "DC-1" to a Domain Controller, setting up a new forest (e.g., mydomain.com).
  - Back in the Server Manager Dashboard we'll select "Promote this ever to a domain controller". Once we're in the Configuration Wizard window we'll select "Add a new forest" and make our "Root domain name:" "mydomain.com". We'll move to the next page; once there, we need to give a password for "Directory Services Restore Mode (DSRM)"; we'll use "Password1234". If it was already preselected, we'll deselect "Create DNS delegation" on the next page. We'll select "Next" through the following pages until we reach the "Prerequisites Check" page. Once there, we'll select "Install". As it finishes installing, it will force a restart of the VM.
![Capture](https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/04b7baec-fed6-4816-a1e3-1edb89bbfef0)
<img src="https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/16834945-23a4-48af-9538-ca8af4d1097c" width="500" />
<img src="https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/875a793e-76a9-4616-82d3-1de599f70b09" width="500" />
<img src="https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/39f5504e-70e7-425d-bd68-a6bc035a2054" width="500" />
<img src="https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/011d03ef-bde1-4e4f-93a8-d27e3997b698" width="500" />
<br>
<br/> 

- Restart "DC-1" and log in as mydomain.com\labuser.
  - We'll log back into DC-1, but now that DC-1 is a Domain Controller, we must log in with the Fully Qualified Domain Name (FQDN). We used "mydomain.com" as our FQDN, so we'll log in by using "mydomain.com\labuser" as our Username and "Password1234" as our Password.
<img src="https://github.com/Kelsow96/Deploying-Active-Directory-And-Creating-Users/assets/169297569/bc790900-08be-4601-8796-ca53533631ea" width="400" />
<br>
<br/>

4. Create Admin and Normal User Accounts in AD
  - Create User Accounts:
    - In ADUC, create Organizational Units (OUs) "_EMPLOYEES" and "_ADMINS".
      - Within our Server Manager Dashboard we will select "Tools" and then "Active Directory Users and Computers". Right-click "mydomain.com" navigate to "New" and select "Organizational Unit (OU)". We will create a _EMPLOYEES OU and a _ADMINS OU.
![Capture](https://github.com/Kelsow96/-Implementing-Active-Directory-On-Premises-in-Azure/assets/169297569/3511a302-7362-43be-95f7-37851f4ed4af)
![Capture](https://github.com/Kelsow96/-Implementing-Active-Directory-On-Premises-in-Azure/assets/169297569/79b28d27-6512-4af0-a79f-10782a3a184a)
<img src="https://github.com/Kelsow96/-Implementing-Active-Directory-On-Premises-in-Azure/assets/169297569/1bd8bdb1-7332-499e-ac25-58eae173da3b" width="300" />
<img src="https://github.com/Kelsow96/-Implementing-Active-Directory-On-Premises-in-Azure/assets/169297569/fcb4ff0a-5cbc-4913-b401-e4e913355668" width="300" />
<br>
<br/>

- Create a new user "jane_admin" and add to the "Domain Admins" group.
  -Right-click "_Admins", navigate to "New" and select "User". Within the new window, we'll create a new user name jane doe make their "User logon name" "jane_admin". Once all of the required fields are filled out we'll select "Next". In the next window, we'll set the password. The password will be "Password1234". We'll also deselect "User must change password at next logon" and select "Password never expires". Once all required fields are filled out, we'll select "Next" and "Finish" on the following page. Now right-click "jane doe" user account and select "Properties". In the new window, select "Member of" and then select "Add" on that page. Within the "Select Groups" window we'll type "Domain Admins" in the "Object Names" text window. You can select "Check Names" to see if the group you want to add to exists within the system. Select "OK" and then "Apply" to finalize adding "jane doe" user account to the "Domain Admins" group.
<img src="https://github.com/Kelsow96/-Implementing-Active-Directory-On-Premises-in-Azure/assets/169297569/cc06ae9d-df38-4623-84f4-826f29c15b4f" width="400" />
<img src="https://github.com/Kelsow96/-Implementing-Active-Directory-On-Premises-in-Azure/assets/169297569/de08889e-61f3-431e-8c60-d909e3090592" width="400" />
<img src="https://github.com/Kelsow96/-Implementing-Active-Directory-On-Premises-in-Azure/assets/169297569/5204780c-d827-4a83-9b6f-7c75a267eddf" width="400" />
<img src="https://github.com/Kelsow96/-Implementing-Active-Directory-On-Premises-in-Azure/assets/169297569/90bb4cbc-ce6b-4d93-bd0f-ea16f11ebbfc" width="400" />
<img src="https://github.com/Kelsow96/-Implementing-Active-Directory-On-Premises-in-Azure/assets/169297569/f2f6d4b8-4c9e-41bb-8d97-91deb4c29c7e" width="400" />
<img src="(https://github.com/Kelsow96/-Implementing-Active-Directory-On-Premises-in-Azure/assets/169297569/7073fadf-40f3-4c45-a7ec-d9f091feada0" width="500" />
<img src="https://github.com/Kelsow96/-Implementing-Active-Directory-On-Premises-in-Azure/assets/169297569/023299de-7ad3-4e6f-9e22-b773911f43c6" width="400" />
<img src="https://github.com/Kelsow96/-Implementing-Active-Directory-On-Premises-in-Azure/assets/169297569/9e582403-08c6-4b17-8668-6f27f37e35ee" width="400" />
<img src="https://github.com/Kelsow96/-Implementing-Active-Directory-On-Premises-in-Azure/assets/169297569/444d6f7c-4b48-4813-952d-856d7cca7c1e" width="400" />
<br>
<br/>

- Log in as mydomain.com\jane_admin.
  - We'll log out of connection with DC-1 by typing "logoff" in Powershell. Once we're logged out, we'll RDP back into DC-1 using our new admin user account "jane_admin"
![image](https://github.com/Kelsow96/-Implementing-Active-Directory-On-Premises-in-Azure/assets/169297569/9f0855dc-cab6-4fa3-8172-0285709ab9ee)
<img src="https://github.com/Kelsow96/-Implementing-Active-Directory-On-Premises-in-Azure/assets/169297569/03d3f61b-b5e4-4284-baed-50f464e0e0bc" width="400" />
<br>
<br/>

5. Join Client-1 to the Domain
  - Configure DNS and Join Domain:
    - Set "Client-1" DNS to "DC-1"’s Private IP in Azure Portal.
    - Restart "Client-1".
    - Join "Client-1" to the domain and restart.
    - Verify "Client-1" in ADUC.
