<p align="center">
<img width="80%" height="80%" alt="Active Directory Microsoft" src="https://github.com/user-attachments/assets/e30f3e4e-1c04-4620-b7c5-4001a8dc4704" />
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />


<h2>Video Demonstration</h2>

- ### [YouTube: How to Deploy on-premises Active Directory within Azure Compute](https://www.youtube.com)

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>Creating Your First Azure Account, Resource Group, and Azure Storage Account Steps</h2>

- Create a free subscription: https://azure.microsoft.com/en-us/free/
(or create a Pay As You Go)
- Step 1: Get logged into the Azure Portal: https://portal.azure.com
- Step 2: Within the Azure Portal, create a "Resource Group".
- Step 3: Create a "Storage Account" within the same "Resource Group" created in previous step.

<p align="center">
Both Resources Should Look Like This:
<img width="758" height="473" alt="image" src="https://github.com/user-attachments/assets/275f4117-6028-44ed-a9c5-d46a705ae204"/>

- Step 4: Create a text file on your local desktop. (For Mac users, go to preferences and set it to plain text.)
- Step 5: Upload the text file you created to your Azure Storage Account.
- Step 6: With the addition of uploading, you can now edit the file within the Storage Account within Azure.
- Step 7: Download the file and you would be able to observe the file on your local desktop.

<h2>Deployment and Configuration Steps</h2>
<h3>Step 1: Setup Resources in Azure</h3>

- Create two virtual machines
	- If you need help creating your virtual machines, please see my tutorial [here](https://github.com/roslyndwilliams/virtual-machine)
	- The first virtual machine will be the Domain Controller
		- Name: DC-1
		- Image: Windows Server 2022
		- Take note of the virtual network (vNet) that is automatically created
       
<p align="center">
<img width="758" height="473" alt="Jan 30, 2026, 07_28_26 AM" src="https://github.com/user-attachments/assets/d624a341-7563-47e4-8130-17fcd2ef365b" />



	- Set DC-1's Virtual Network Interface Card (vNIC) private IP address to be static
		- Go to DC-1's network settings
		- Select Networking
		- Select the link next to Network Interface
		- Select IP Configurations > ipconfig1
		- Change the assignment from dynamic to static 
			- This ensures DC-1's IP address will not change
	   
<p align="center">
<img width="70%" height="70%" alt="Jan 30, 2026, 08_49_10 AM" src="https://github.com/user-attachments/assets/22ace19e-8b9d-4605-abe6-a8b2566a7935" />
<img width="70%" height="70%" alt="Jan 30, 2026, 02_06_18 PM" src="https://github.com/user-attachments/assets/729ea6cd-9b42-4c4a-94a6-1b46a2c511db" />
<img width="70%" height="70%" alt="Jan 30, 2026, 02_35_20 PM" src="https://github.com/user-attachments/assets/b2dc7d4c-e669-43fe-8cc3-b0e0ceb77877" />

</p>


	- The second virtual machine will be the Client
		- Name: Client-1
		- Image: Windows 10 Pro
		- Use the same resource group and vNet as DC-1

<p align="center">
<img width="70%" height="70%" alt="Jan 30, 2026, 03_00_06 PM" src="https://github.com/user-attachments/assets/ee5cf9c6-a987-4119-89b0-3cf27b219045" />
<img width="70%" height="70%" alt="Jan 30, 2026, 03_01_28 PM" src="https://github.com/user-attachments/assets/f80053c1-8623-425f-96f6-cd6550151ef6" />


<h3>Step 2: Ensure Connectivity Between the Client and Domain Controller</h3>

- Login to Client-1 using Microsoft Remote Desktop
- Search for Command Prompt and open it
- Ping DC-1's private IP Address (for example, 10.1.0.4)
	- Type "ping -t 10.1.0.4" into the command-line interface
	- The ping request continually  times out due to the firewall settings
		- To fix this, we need to enable ICMPv4 on DC-1's local Windows firewall

<p align="center">
<img width="70%" height="70%" alt="68747470733a2f2f692e696d6775722e636f6d2f5536554f716a352e706e67" src="https://github.com/user-attachments/assets/f83028b0-be77-4da5-b083-b0052ccc80ef" />

	
- Login to DC-1 using Microsoft Remote Desktop
	- Start > Windows Administrative Tools > Windows Defender Firewall with Advanced Security > Inbound Rules
	- Sort the list by protocols
	- Find "Core Networking Diagnostics" and "ICMPv4" and enable these two inbound rules

<p align="center">
<img width="50%" height="50%" alt="Networking Diagnostics settings on Windows" src="https://github.com/user-attachments/assets/c5c9bbe5-fb04-4f3a-a1ef-4f417f14e242" />
<img width="80%" height="80%" alt="68747470733a2f2f692e696d6775722e636f6d2f4259314f6867622e706e67 copy" src="https://github.com/user-attachments/assets/a2a6b990-c6ac-405b-bbf2-a1ed16055c2f" />

</p>

- Log back into Client-1 and the command line will automatically begin pinging DC-1 successfully
    
<p align="center">
<img width="70%" height="70%" alt="68747470733a2f2f692e696d6775722e636f6d2f38393057494a422e706e67" src="https://github.com/user-attachments/assets/808a92c9-1b66-48e8-ad61-6215a0d41a1d" />


<h3>Step 3: Install Active Directory</h3>

- Log back into DC-1
	- Open Server Manager
	- Select "Add Roles and Features" > Follow the prompts
	- At Server Roles, check "Active Directory Domain Services."
		- Ignore how the picture below already says "Installed"
	- Select Add Features > select Next
	- Complete the installation

<p align="center">
<img width="80%" height="80%" alt="68747470733a2f2f692e696d6775722e636f6d2f445152564e6e6d2e706e67" src="https://github.com/user-attachments/assets/6eac62d2-945e-4408-ad4b-56d914d35d00" />
<img width="50%" height="50%" alt="68747470733a2f2f692e696d6775722e636f6d2f52707a6e6752692e706e67" src="https://github.com/user-attachments/assets/e72f8fc2-c005-4ad7-bf05-23cc1248a7c1" />

</p>

- At the top right of the Server Manager Dashboard, click on the flag
- Select "Promote This Server to a Domain Controller"

<p align="center">
<img width="70%" height="70%" alt="68747470733a2f2f692e696d6775722e636f6d2f474f59695446652e706e67 copy" src="https://github.com/user-attachments/assets/0573ea2e-cb30-4ae3-b75c-6af2a7d728a8" />
	
 - Select "Add a New Forest"
 	- Root domain name: mydomain.com
- Select Next
- Create a password
- Select Next and follow the prompts
- Select Install to complete the installation


<p align="center">
<img width="70%" height="70%" alt="68747470733a2f2f692e696d6775722e636f6d2f496a66555a30612e706e67 copy" src="https://github.com/user-attachments/assets/4662fa2e-4f5d-4678-89a9-10c63b450fdc" />
	
- DC-1 will automatically restart
- Log back into DC-1 as user: mydomain.com\labuser               

<p align="center">
<img width="70%" height="70%" alt="68747470733a2f2f692e696d6775722e636f6d2f6f4e703339444b2e706e67 copy" src="https://github.com/user-attachments/assets/efc7af4a-659b-47f7-8a93-1a8b1a8f5564" />
	


<h3>Step 4: Create an Admin and Normal User Account in Active Directory v1.15.8</h3>
     
- On DC-1, open Server Manager
- Click Tools at the top-right of the screen
- Select Active Directory Users and Computers

<p align="center">
<img width="70%" height="70%" alt="68747470733a2f2f692e696d6775722e636f6d2f756447486247732e706e67 copy" src="https://github.com/user-attachments/assets/c615bf2b-8639-498b-875c-b89a0615a146" />
	
- Right-click mydomain.com > New > Select Oranizational Unit (OU)
- Create two OUs
	- Name the first "_EMPLOYEES"
	- Name the second "_ADMINS"
	
<p align="center">
<img width="70%" height="70%" alt="68747470733a2f2f692e696d6775722e636f6d2f3577535a7541342e706e67 copy" src="https://github.com/user-attachments/assets/3799dbeb-0c90-4e83-8feb-84f40c2a8987" />
	
	
- Right-click mydomain.com and click Referesh to sort the new organizational units to the top
- Go to the _ADMINS OU
- Right-click the name of the OU > New > User
	- First/Last name: Jane Doe
	- User login name: jane_admin
	- Select Next
	- Create a password
	- Uncheck all boxes
	- Select Next and then select Finish
<p align="center">
<img width="70%" height="70%" alt="68747470733a2f2f692e696d6775722e636f6d2f6e76366a6339702e706e67 copy" src="https://github.com/user-attachments/assets/6d8584cf-a57e-4749-ad67-5ca8af3b4c1d" />
<img width="70%" height="70%" alt="68747470733a2f2f692e696d6775722e636f6d2f754c6f7051545a2e706e67" src="https://github.com/user-attachments/assets/ab6cf60f-3bcd-48f7-a66e-c2c9c93f92f9" />
	
- Go to the _ADMINS OU
- Right-click Jane Doe > select Properties
	- Click the tab named "Member of" > select Add
	- Type in the names of your domain administrators
	- Select "Check Names" > OK > Apply
- Log out of DC-1 as "labuser" and log back in as “mydomain.com\jane_admin”



<p align="center">
<img width="70%" height="70%" alt="68747470733a2f2f692e696d6775722e636f6d2f4561704d6842732e706e67" src="https://github.com/user-attachments/assets/276a16ef-eb97-474b-a805-51da2db6ab44" />
<img width="70%" height="70%" alt="68747470733a2f2f692e696d6775722e636f6d2f764762384b78382e706e67" src="https://github.com/user-attachments/assets/54c6309c-c0c8-41e7-8f70-4726c6ae7bb0" />

</p>
 
     

<h3>Step 5: Join Client-1 to your domain (mydomain.com)
</h3>

- Go back to the Azure portal
- Navigate to the Client-1 Virtual Machine
- On the left-hand side of the screen select Networking
- Select the link next to the NIC > select DNS Server > Custom
- Type in DC-1's private IP address
- Click Save
- After it is done updating, select Restart and select Yes

<p align="center">
<img width="70%" height="70%" alt="68747470733a2f2f692e696d6775722e636f6d2f7a365565734f372e706e671" src="https://github.com/user-attachments/assets/82e54f36-0a5c-483d-8a9f-0ffbe4babf73" />
<img width="70%" height="70%" alt="68747470733a2f2f692e696d6775722e636f6d2f627430794b31372e706e672" src="https://github.com/user-attachments/assets/b91a4154-def9-494f-80fb-b806e767d237" />
<img width="70%" height="70%" alt="68747470733a2f2f692e696d6775722e636f6d2f734235656448352e706e673" src="https://github.com/user-attachments/assets/cf75fb18-c70e-4dd4-af7d-8d9fc612c81c" />
</p>

- Log back into Client-1 using Microsoft Remote Desktop as the original local admin (labuser)
- Right-click the Start menu and select System
- On right-hand side of the screen, select Rename This PC (Advanced) > Change
- Under "Member of," select Domain
- Type "mydomain.com" and select OK
	- Username: mydomain.com\jane_admin
	- Type in password and press OK
- Restart the computer 			


<p align="center">
<img width="80%" height="80%" alt="68747470733a2f2f692e696d6775722e636f6d2f3348784a4c70652e706e674" src="https://github.com/user-attachments/assets/5a35fbbc-34c2-4703-97c2-6796d0bfc8b5" />
<img width="70%" height="70%" alt="68747470733a2f2f692e696d6775722e636f6d2f4a384d347a42552e706e674" src="https://github.com/user-attachments/assets/2a684753-8c5d-4dbc-89ef-e3932966e77d" />
</p>

<h3>Step 6: Setup Remote Desktop for non-administrative users on Client-1
</h3>

- Log back into Client-1
- Use mydomain.com\jane_admin
- Right-click the Start menu and select System
- On the right-hand side of the screen, select Remote Desktop
- Under User Accounts, click "Select Users That Can Remotely Access This PC > select Add
- Type in the name of your domain users
- Select "Check Names" > OK > OK

 
 <p align="center">
<img src="https://i.imgur.com/HgAXVMX.png" height="70%" width="70%" alt="Azure Free Account"/> <img src="https://i.imgur.com/0QDUk5l.png" height="60%" width="60%" alt="Azure Free Services"/>
</p>

<h3>Step 7: Create as many additional users as you would like and attempt to log into Client-1 with one of the users' profiles
</h3>

- Log back into DC-1 as jane_admin
- Search for "Powershell_ise,"
- Right-click on Powershell_ise and open it as an administrator
- At the top-left of the screen select New Script and paste the contents of the following script into it
	- You can find the script [here](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1)

<p align="center">
<img src="https://i.imgur.com/MpvLIbB.png" height="70%" width="70%" alt="Azure Free Account"/> <img src="https://i.imgur.com/V4vIvre.png" height="70%" width="70%" alt="Azure Free Services"/>
</p>

- Click the green arrow button near the top-middle of the screen
	- This will run the script
- Once the users have been created, go back to Active Directory Users and Computers > mydomain.com > _EMPLOYEES
		- You will see all the accounts that were created
- You can now log into Client-1 with one of the accounts that were created
	- Try logging into Client-1 as user "base.milu" using the password "Password1"

<p align="center">
<img src="https://i.imgur.com/3HN1Nf4.png" height="80%" width="80%" alt="Azure Free Account"/> <img src="https://i.imgur.com/CeE8LGh.png" height="50%" width="50%" alt="Azure Free Services"/>  <img src="https://i.imgur.com/7ZVBp8a.png" height="70%" width="70%" alt="Azure Free Services"/>
</p>

<p align="center">
<img src="https://i.imgur.com/EzgHWRs.png" height="70%" width="70%" alt="Azure Free Account"/> <img src="https://i.imgur.com/hYFodxu.png" height="70%" width="70%" alt="Azure Free Services"/>
</p>



🎉Congratulations! You have implementated on-premises Active Directory and created users within an Azure virtual machine!🎉
