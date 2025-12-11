<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 11 (25H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Step 1: Plan and Prepare the Environment
- Step 2: Install and configure Windows Server roles
- Step 3: Promote the Server to a Domain Controller
- Step 4: Post-Deployment Configuration and Validation
- Step 5: Create and Organize User Accounts and Groups
- Step 6: Define and Configure Group Policy Objects (GPOs)
- Step 7: Link GPOs to Organizational Units (OUs)
- Step 8: Monitor, Update, and Maintain Policies and Accounts

<h2>Script (For later use in the tutorial)</h2>

[Script](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1)

<h2>Deployment and Configuration Steps</h2>

<p>
<img width="1599" height="899" alt="image" src="https://github.com/user-attachments/assets/d62e4081-9997-4000-82bf-4eb3eebdd8dc" />
</p>
<p>
Within Azure, create a resource group, only giving it a name and a region 
</p>
<br />

<p>
<img width="1599" height="899" alt="Screenshot 2025-12-11 143710" src="https://github.com/user-attachments/assets/8078f4f6-6992-426b-8f6b-aa09f66fa3f4" />
</p>
<p>
Create a virtual network > place in the previously created resource group > name  the virtual network > then review and create 
</p>
<br />

<p>
<img width="1599" height="899" alt="image" src="https://github.com/user-attachments/assets/76d937c0-b03b-4307-b8b1-656b75fc2206" />
</p>
<p>
To create a domain controller, first select create virtual machine > name virtual machine > assign VM to the same region as the virtual network > for the image select windows server 2022 > choose an appropriate size (for tutorial 2 vcpus and 8 GIB) > Set username and password > Under licensing check mark both boxes > then go to the network tab and put the vm within the previously created vnet > Review and create
</p>
<br />

<p>
<img width="1599" height="899" alt="image" src="https://github.com/user-attachments/assets/570628e3-9efa-4450-8b13-1250a5cb8e84" />
</p>
<p>
To create the client Vm select, create virtual machine > assign to the previously create resource group > name the vm > assign to the same region in previously used > select windows 11 pro for the image > choose appropriate size (for tutorial 2 vcpus and 8 GIB) > Set username and password > check the box under licensing > then go to the networking tab and assign the VM to the previously created vnet > Review and create
</p>
<br />

<p>
<img width="1599" height="899" alt="image" src="https://github.com/user-attachments/assets/31e57e77-9a59-4fcf-867f-b89ea1d478cc" />
</p>
<p>
Once they are both deployed, go to your domain controller > network settings > click on the network interface card > select ipconfig1 > then set the allocation from dynamic to static and save
</p>
<br />

<p>
<img width="1599" height="899" alt="image" src="https://github.com/user-attachments/assets/1fb30015-0f83-4703-9949-938796b7dab7" />
</p>
<p>
For the tutorial, we will disable the Windows firewall just for testing > within azure, copy the public IP address of the domain controller > open Remote Desktop and log in to the domain controller > Right-click the Start menu and run “wf.msc.” > Select Windows Defender Firewall Properties > turn off the firewall for both “Domain Profile, Private Profile, and Public Profile” > Apply and OK
</p>
<br />

<p>
<img width="1599" height="899" alt="image" src="https://github.com/user-attachments/assets/e9747d3b-2be0-4979-8bca-911c160123bb" />
</p>
<p>
Go to virtual machines > domain controller VM to find the private IP address (click the VM and scroll down) > now go to the client VM > go to network settings > open the interface card > DNS servers > change from virtual network to custom > paste the private IP from the domain controller > and save
</p>
<br />

<p>
<img width="1599" height="899" alt="image" src="https://github.com/user-attachments/assets/06128c0c-aa57-4e22-b4e0-d52dbca222cb" />
</p>
<p>
Go to virtual machines and restart the client VM > log in to client VM in remote desktop > go back to Azure and copy the Domain Controllers private IP > open PowerShell in the VM > then type “ping (domain controller private ip) it should show replies in the ping (if not go back and check to see if the windows firewalls are disabled or if you inputed an incorrect private IP in the client VM's NIC) > now run ipconfig /all (this should show the private IP address of the domain controller next to DNS Servers) 
</p>
<br />

<p>
<img width="1599" height="899" alt="image" src="https://github.com/user-attachments/assets/d930d55d-b25b-43be-add8-50b706729c9b" />
</p>
<p>
Logged into the domain controller > click Start, and find the Server Manager application > select Add Roles and Features > select Next twice > then confirm the destination server is the one created and select Next > in server roles, check box Active Directory Domain Services and click Add Features > click Next until you reach the page that says confirm installation selections and check the box "Restart the destination server automatically if required" and install > once installed you can close the tab
</p>
<br />

<p>
<img width="1599" height="899" alt="image" src="https://github.com/user-attachments/assets/dc515d29-9447-452b-938c-b17c83feb55a" />
</p>
<p>
Within Server Manager, select the flag icon > select promote this server to a domain controller > select add a new forest and add a Root domain name (example for the tutorial: mydomain.com) > select next and set a Directory Service Restore Mode password > select next and make sure the box for Create DNS delegation is unchecked > select next until prerequisites check and install > then click install when on the installation tab (this will restart the VM) > Restart and log back in to the domain controller with the username mydomain.com\(previous username for the domain controller) (Simply put, logging in as a domain user since the vm was turned into a full fledged domain controller)
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
In the domain controller VM, click Start and look for the folder Windows Administrative Tools, then click the dropdown > open Active Directory Users and Computers > right click mydomain.com > go to new and select Organizational Unit  and name it “_EMPLOYEES” > and select ok > now repeat the previous step this time naming it “_ADMINS” > click the _ADMINS folder then right click the white area to the right and choose new then user > create a user (Name, Last Name, and User logon name) then click next set password and check the box Password never expires (for the tutorial) > click next and finish
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Right-click the created user, go to Properties > Member Of > Add > type in the box “domain admins,” and select check names (this will find the built-in domain admins group) > select ok > apply > ok (This portion gives the created user admin permission) > Now log out of the domain controller and reconnect to the domain controller this time using mydomain.com\(created admin username) as the username
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Now, within the client VM, right-click the Start menu and select System > select advanced system settings > on the computer name tab, click Change > under Member of, type in mydomain.com, and click ok > Now, log in with the created admin login info (from the previous step). > Click ok on the welcome message, click close, and restart now (this will restart the client VM)
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Log in to the domain controller with the admin user login > open Active Directory Users and Computers > to verify that the client user was added to the domain, select Computers (You will see that it has been added to the domain) > create a new organizational unit called “_CLIENTS” by right-clicking mydomain.com, New, and organizational unit > now, in Computers, drag the client user into the “_CLIENTS” folder.
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Within the client VM, logged in with the admin user login, right-click Start, and go to System > scroll down and click Remote Desktop > Remote desktop users > click add and type in domain users and click ok (this will allow all domain users to log in to that device) (now you can log into the client vm with your normal login from the beginning of the tutorial)
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Log in to the Domain controller VM with the admin user login info > in the Start menu, search for PowerShell ISE and run as an administrator > now copy the script at the top (Used to create random users) > now in PowerShell, create a new file and paste the script into the file within PowerShell > click the run button (this will start the creation of 10000 users into the _EMPLOYEES Folder) 
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Now open Active Directory Users and Computers > right-click the _EMPLOYEES folder and refresh > within the folder pick any name/username that was created with the script > now log in to the client VM using mydomain.com\(picked name/username) and password is Password1 due to the script default 
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Log in to the domain controller VM with Admin login info > right-click Start and click Run > type gpms.msc > in  GPMC click Default Domain Policy > right click and go to computer configuration, policies, Windows Settings, Security, Account policies, Account Lockout Policy > Set lockout duration for 30 minutes > Set account login threshold to 5 > Enable Allow admin account lockout > Set account lockout counter after 10 min > now log in to the client VM as the admin and open command prompt (CMD) > type in “gpupdate /force” (updates the policy instead of waiting for an automatic update)
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Log in to the domain controller VM with the admin login info > open Active Directory Users and Computers > click on employees and pick a random user (or use previously picked user) > now attempt to log into the client VM with the user name mydomain.com\(picked random user) with an incorrect password 6 times > now on the domain controller Active Directory Users and Computers, right-click mydomain.com and click find and search the random user you attempted to login with > once found double click it and go to account (Next to unlock account there will be a message relaying that the account is locked) > check box unlock account and apply > now login into the client VM with the user you just unlocked (Password is Password1 for the User) 
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
In the domain controller, find the user and disable the account > log out of the client VM and attempt to log in again > go back to the domain controller and enable the account > log back into the user in the client VM
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
In the domain controller, click Start and search “eventvwr.msc” > click the drop-down next to Windows logs > right click security and click find to search for the created user previously used > then observe the history of the account > now log in to the client VM and in the Start menu search “eventvwr.msc” and run as administrator > login to the pop-up with the admin login from earlier > go to windows logs, security right-click and click find to search for the created user (Admin side shows more information like login failures and account unlocks)
</p>
<br />

