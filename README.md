<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Azure logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Powershell ISE Script
- Active Directory Domain Services

<h2>Operating Systems Used </h2>

- Windows 10</b> (21H2)
- Windows Server 2022

<h2>Installation Steps</h2>

<h2>Creating Virtual Machines in Microsoft Azure</h2>
<p>
<img src="https://imgur.com/wUFZnxu.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
In Microsoft Azure, create two virtual machines that run on Windows 10 (Client-1) and Windows Server (Domain Controller or DC). In the virtual machines, I made sure to keep the region, virtual machine size, and virtual network (vnet) were the same to ensure that both machines are running on the same resources to communicate and drive traffic.
</p>
<br />
<h2>Changing the Domain Controller's Network Interface</h2>
<p>
<img src="https://imgur.com/IKA26tB.png" height="80%" width="80% alt="Disk Sanitization Steps"/>
</p>
<p>
After creating the virtual machines, change the Domain Controller's (DC) Network Interface (NIC) to static inside of Microsoft Azure. I have toggled to Networking under the DC-virtal machine --> Click on the virtual NIC --> Click on IP configurations --> Edit the assignment to "Static" --> Click on Save. Changing the NIC to static allows the IP address to stay the same and not change if a computer pulls the IP address.
</p>
<br />
<h2>Viewing Topology</h2>
<p>
<img src="https://imgur.com/oJ72bf9.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
After creating both machines, reviewed the topology to showcase how both machines were running from the same Vnet and subnet mask. Also, we can see that each virtual machine has its own IP addresses and network security groups.
</p>
<br />
<h2>Reviewing Connectivity between DC and Client 1</h2>
<p>
<img src="https://imgur.com/vMMg3os.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
After logging into the DC and Client 1, complete a perpetual ping to DC's prviate IP address on Client 1 to observe the connectivity. The connectivity has failed due to DC's firewall blocking the ICMP protocols. Thus, go back to the DC remote desktop and enabled the ICMP4s protocols for "Echo Request" for the pings to respond. After enabling the ICMP4 protocol, toggle back to Client 1 to review that the ping to DC's private IP address was successful.
</p>
<br />
<h2>Install Active Directory</h2>
<p>
<img src="https://imgur.com/DmOn0oJ.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
After reviewing the connectivity between both machines, install Active Directory on the domain controller by opening Server Manager --> Clicking on "Tools" --> Clicking on "Active Directory Users and Computers" --> Clicking on "Add Roles and Features" and starting the setup wizard. Under "Server Roles", select "Active Directory Domain Services" and complete the remainder of the setup wizard. Once the installation is complete, the notifications flag will have a hazard notification next to it.
</p>
<br />
<h2>Continue Installing Active Directory</h2>
<p>
<img src="https://imgur.com/Gx6TRrF.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
After clicking on the notification flag, click on "Promote this server to a domain controller" to continue setup of the Active Directory. Next in the Deployment Confirguration Wizard, select "Add a new forest" and create a domain name. Continue to install and setup in the configuration wizard and ensure to allow the "NETBIOS" to load with previously created domain name. After the installation is complete, DC will restart itself and sign back in with DC VM login credentials.
</p>
<br />
<h2>Create Administrator and User Accounts in Active Directory</h2>
<p>
<img src="https://imgur.com/TtbI8ql.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
After logging in, open Server Manager and toggle to "Active Directory Users and Computers" --> right-click under "domain.com" --> Click on "New" and "Organizational Unit" to create "ADMINS" and "EMPLOYEES folders in Active Directory. Once the "Admin" folder is created, right-click on "New" and "User" to add "Jane Doe" to user group.
</p>
<br />
<h2>Continue creating Administrator and User Accounts in Active Directory</h2>
<p>
<img src="https://imgur.com/k31BFyT.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
Once "Jane Doe has been created, add "Jane Doe" to the "Domain Admins" group to allow access to login as an administrator. Next, log out and login back in as "Jane Doe" under "domain.com\createdjanedoeuser and use the account to continue to join client 1 with the DC.
</p>
<br />
<h2>Join Client 1 to Domain Controller</h2>
<p>
<img src="https://imgur.com/06vkRgk.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
After logging back in DC as "Jane Doe", toggle back to DC in Microsoft Azure portal and change the DNS server settings. First, copy the DC's private IP address. Click on the Client Virtual Machine and click on "Networking" --> Click on the virtual NIC --> Click on DNS server --> Click on "Custom" and paste DC's private IP address --> Click on "Save. Next, Client 1 will restart itself and log back in to Client 1.
</p>
<br />
<h2>Continue joining Client 1 to Domain Controller</h2>
<p>
<img src="https://imgur.com/Jb8ZBz4.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
Next, remote desktop into Client 1 and open System properties. Right-click on "Windows" --> click on System --> Select "Rename this PC", --> click on "Change" for "Rename this computer or change its domain" --> Select "domain", add "domain.com", and select "OK" to add domain. Next, verify that Client 1 displays under "Computers" folder in Server Manager on the Domain Contoller.
</p>
<br />
<h2>Setup Remote Desktop to Non-Administrative Users</h2>
<p>
<img src="https://imgur.com/L5jcTeg.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
Next, we will add non-administrative users to remotely log in and gain access on other computers. Right-click on "Windows" --> click on System --> Select "Remote Desktop", --> click on "Add", type in "domain users", and click on "Check Names" --> Select "OK". Next, toggle back to DC as "Jane Doe" login.
</p>
<br />
<h2>Create Additional Users</h2>
<p>
<img src="https://imgur.com/p5KB13G.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
After logging into Dc, open and run Powershell ISE as an administrator. Copy and paste created script to create additional users in the employees file under Active Directory. After pasting the script, select "RUN" (green play notification) to start the creation of users. Also, the powershell script will create a number of random user names and that will use the login credential of any password that is set.
</p>
<br />
<h2>Logging in as User</h2>
<p>
<img src="https://imgur.com/T4YkCeq.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>
</p>
<p>
After the additional names are created, we can login as a user and verify that the client computer provides access to non-administrative users.
</p>
<br />
