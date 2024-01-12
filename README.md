<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1 align = "center">Installing and Configuring Active Directory</h1>
This tutorial demonstrates how to install and configure Active Directory using Azure. We will be using two virtual machines on Azure that are on the same virtual network. One virtual machine will be installed with Active Directory and configured to be the Domain Controller and other virtual machine will be used as a Client. Then, we will configure the Active Directory to allow the Client to join the domain as well as creating user accounts using a Powershell script. 
</p>

<h3>Environments and Technologies</h3>

<li>Microsoft Azure (Virtual Machines/Compute)</li>
<li>Remote Desktop</li>
<li>Active Directory Domain Services</li>
<li>Powershell</li>
<li>Notepad (Optional): for writing down usernames and passwords for virtual machines</li>
</ul>


<h3>Operating Systems</h3>

<li>Windows Server 2022</li>
<li>Windows 10 Pro (21H2)</li>
</ul>


<h3>Installation Steps</h3>

<p>
<h3>Creating Resources in Azure</h3>

<p>
</p>
Go to Azure Portal, create a virtual machine for the Domain Controller, name it DC-1
</p>
<img src="https://i.imgur.com/8ryNTmy.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<p>
Create a second virtual machine for the Cient and name it Client-1, the vms should be on the same virtual networks. Give the resources some time to load, approximately five to ten minutes
<p>
<img src="https://i.imgur.com/FODGjhk.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<p>
Go to DC-1 click on Networking under Setttings, click on the Network Interface link. Go to IP configurations under Settings. Click on ipconfig link to open up a window, change the IP Configuration to Static under Allocation
<p>
It being dynamic will make it difficult for the vm to communicate with our client vm
<p>
<img src="https://i.imgur.com/lhj8Kl1.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<p>
</p>

<br />

<h3>Ensuring Connectivity</h3>

<p>
<p>
Connect to Remote Desktop with Client-1 public ip address. Open command prompt and enter the command ping -t [DC-1 private ip address]. To send endless ping in order ensure reachability with the Domain Controller. Connection should time out after the first ping due to the Domain Controller's Firewall Settings.
<p>
<img src="https://i.imgur.com/7xYDABl.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<p>
Log into DC-1 vm, click start and open wf.msc in search bar to open windows defender firewall and advanced security. Go to Inbound Rules and enable the rules under the protocol ICMPv4, specifically Core Networking Diagnostics - ICMP Echo Request (ICMPv4-In).
<p>
<img src="https://i.imgur.com/hYBsgq0.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
<p>
Go to Client-1 vm and you should be able to see replies. Enter Ctrl C to end traffic
<p>
<img src="https://i.imgur.com/AxMjWDC.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<p>
</p>

<br />

<h3>Installing Active Directory on the Domain Controller</h3>

<p>
In DC-1, go to the Server Manager Dashboard and click on Add Roles and Features. Go through the installation process, at Server Roles check the box for Active Directory Domain Services.
<p>
<img src="https://i.imgur.com/PzoHssP.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<p>
When installed, we now have to promote the server into a domain controller. You may notice a warning notification on the top right where the flag icon is. Click on that flag and click Promote this server to a domain controller. Click on Add a new forest and specify a domain name. For this tutorial, we'll name the domain: mydomain.com, specifiy the password, and proceed with the install. You will be automatically signed out, re-log in through Remote Desktop, and the installation is fully completed.
<p>
<img src="https://i.imgur.com/je6ps2Q.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<p>
<img src="https://i.imgur.com/jMkYyS8.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<p>
When logging into DC-1 through RDP, rememeber to login with the correct credentials. For example mydomain.com\labuser
<p>
</p>

<br />

<h3>Configuration Steps</h3>

<h3>Creating Organizational Units and Users</h3>

<p>
Organizational Units act like folders that hold information, privelages, and login access of users in the directory. In the Server Manager dashboard, go to Tools tab and open the Active Directory Users and Computers console, right click on the domain (mydomain.com) and make two OUs, _admins and _employees. These names are needed for a later step where we create multiple accounts. In _admins OU we'll create the user Jane Doe with the username jane_admin and password of your choice.
<p>
<img src="https://i.imgur.com/HNxDcVs.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<p>
Go to the user Jane Doe and open Properties, add her in Member Of as Domain Admins. This is to grant Jane admin privileges.
<p>
<img src="https://i.imgur.com/Yhn7ptF.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<p>
Exit out of the vm, sign back in with username jane_admin
<p>
</p>

<br />

<h3>Joining the Client to the Domain</h3>

<p>
Access the Azure Portal and go to DC-1, go to networking and take note of the private ip address. Repeat the same steps for Client-1 click on the link next to network interface. In DNS servers under Settings, set the DNS server to Custom. Then enter DC-1 private ip address and save. Go to Client vm and restart it to save changes.
<p>
<img src="https://i.imgur.com/hMYOxvV.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<p>
Go to the Client vm, go to Settings then System, About, then Rename this PC (advanced)
<p>
Enter the domain and credentials in order to let the client join the domain (logging in as jane admin). For example, the login credentials have to be input as: mydomain.com\jane_admin
<p>
<img src="https://i.imgur.com/ZMlwMPN.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<p>
Remote Desktop has to be enabled for non administrative users, before users in the domain can use the client computer. While logged in as the administrator (jane_admin), open System Properties. Click on Remote Desktop and Select users that can remotely access this PC, select Add, and enter domain users.
<p>
<img src="https://i.imgur.com/jcb8ZQA.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<p>
</p>

<br />

<h3>Creating Additional Users</h3>

<p>
In DC-1 vm being logged in as jane_admin, open Powershell ISE as administrator. Right click and open this powershell script https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1
<p>
We will create randomly generated accounts all with the password "Password1". In Powershell ISE, create a new file and copy and paste the powershell script into the file, on the third line edit the number of users created from 10000 to 100, then run the script. All these users are generated and put into the _employees organizational unit in the Active Directory.
<p>
<img src="https://i.imgur.com/ueeHHOY.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<p>
Go to Active Directory Users and Computers, select a random username to acquire the login information by going to Properties and in the Account tab. The username generated should appear as [first name].[last name].
<p>
<img src="https://i.imgur.com/yA7Nw8L.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>
<p>
Log into the Client vm using the generated username you have selected (mydomain.com\username) and the password "Password1". If this works everything was done correctly.
<p>
This is the conclusion of the lab
