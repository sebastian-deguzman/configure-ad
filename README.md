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
- Windows 10 (22H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Create 2 Virtual Machines (DC-1 and Client-1)
- Install Active Directory on DC-1 using Windows server 2022
- Join Client-1 VM using windows 10 to the domain of the DC-1 VM  
- Setup remote desktop for non-administrative users on Client-1 VM
- Create additional users and login to Client-1 VM


<h2>Deployment and Configuration Steps</h2>

![Screenshot 2025-04-25 141857](https://github.com/user-attachments/assets/2b5d8144-a4ef-49c0-aaa2-3264839d4daf)

We will create two virtual machines. The first one will be our domain controller named Dc-1 running on the windows server 2022. The other VM we will create will be Client-1 running on windows 10 22H2. Well be adding both VMs to the same network. We will configure Dc-1s ip address to static so it reframes from changing.


Next we will login to Dc-1 through remote desktop (RDP) and disable the windows firewall (for testing connectivity). To do this, while on the Dc-1 vm, windows > run > type wf.msc > windows defender firewall > off. under the same tab we will turn off private and public pofile as well.


Next we will change the DNS server on Client-1 to the static IP address of Dc-1. this can be done in the Azure portal. Once completed we will restart both VMs.


Logged into Client-1 > windows > powershell we ill begin to test the connection. We will ping Dc-1 (ping 10.0.0.4) and then check the configuration by typing ipconfig /all and we should see the DNS server match the domain controller.



Next, on Dc-1 we will install Active Directory Domain Services using the Server Manager.


Next, we will promote Dc-1 to a domain controller and set upa new forest as "mydomain.com" and we can loggin to Dc-1 as "mydomain.com\Labuser"


Next, we will open Active Directory users and computers. Create two OU (organizational Units) called _EMPLOYEES and _ADMINS. We do this by right-clicking mydomain.com, select new and choosing the OU.


Now we will create a new employee named "andrew smith" with  logon name "andrew-admin". To access this right click Admins > new > user. Password will be Password1


