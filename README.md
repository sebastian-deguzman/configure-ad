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

![Screenshot 2025-04-25 152000](https://github.com/user-attachments/assets/82c48612-f6ff-4d03-bbad-c7bbd56e8f82)

Next we will login to Dc-1 through remote desktop (RDP) and disable the windows firewall (for testing connectivity). To do this, while on the Dc-1 vm, windows > run > type wf.msc > windows defender firewall > off. under the same tab we will turn off private and public pofile as well.

![Screenshot 2025-04-25 152652](https://github.com/user-attachments/assets/7e8cc851-99ea-4aea-945b-6d67defb0a3c)

Next we will change the DNS server on Client-1 to the static IP address of Dc-1. this can be done in the Azure portal. Once completed we will restart both VMs.

![Screenshot 2025-04-25 153054](https://github.com/user-attachments/assets/d0481cbd-3dc1-4413-a916-9dbad3ec89a1)

Logged into Client-1 > windows > powershell we ill begin to test the connection. We will ping Dc-1 (ping 10.0.0.4) and then check the configuration by typing ipconfig /all and we should see the DNS server match the domain controller.

![Screenshot 2025-04-25 174451](https://github.com/user-attachments/assets/91e2a9f7-0729-4b1a-8f8c-9c5c5a8fe89b)


Next, on Dc-1 we will install Active Directory Domain Services using the Server Manager.

![Screenshot 2025-04-25 155226](https://github.com/user-attachments/assets/b032f97d-d27d-4479-a01e-457a9ba32030)

Next, we will promote Dc-1 to a domain controller and set upa new forest as "mydomain.com" and we can loggin to Dc-1 as "mydomain.com\Labuser"

![Screenshot 2025-04-25 160358](https://github.com/user-attachments/assets/9aa615b5-48d4-43f2-9baf-0f57acd70f9d)

Next, we will open Active Directory users and computers. Create two OU (organizational Units) called _EMPLOYEES and _ADMINS. We do this by right-clicking mydomain.com, select new and choosing the OU.

![Screenshot 2025-04-25 162746](https://github.com/user-attachments/assets/64db6546-375e-4a9f-8d61-b8480cd6a189)

Now we will create a new employee named "andrew smith" with  logon name "andrew-admin". To access this right click Admins > new > user. Password will be Password1

![Screenshot 2025-04-25 174735](https://github.com/user-attachments/assets/9d13e0ac-cd65-4bd2-ba1a-84fe3ce9a1a9)

Once that is set up we will add andrew as a domain admin. To do this we right click andrew smith > properties > member of > add > enter domain admins > check name > ok

![Screenshot 2025-04-25 165622](https://github.com/user-attachments/assets/f3ece014-9c11-4a04-95ea-bdb11cd2b3e9)

We will log out of Dc-1 and log back in using the credentials of andrew smith (andrew-admin / Password1). This is how we will log in for future logins to Dc-1

![Screenshot 2025-04-25 165901](https://github.com/user-attachments/assets/c1d4f821-c190-426a-8264-63fd7b9f48b2)

Next, we will log in to Client-1 and join the domain. inside of Client-1 > right click windows > system > rename this PC > change > domain: mydomain.com Once completed the VM should restart applying the changes

![Screenshot 2025-04-25 172125](https://github.com/user-attachments/assets/dd0e274d-833a-46d0-9a46-414a394b5908)


We then go back to Dc-1 and open Active Directory users and computers and create another OU (organizational unit) named _CLIENTS undery my domain.com

![Screenshot 2025-04-25 172119](https://github.com/user-attachments/assets/fa6e375a-64b4-4a8f-9cd2-937dd12a84b2)

Next, we log back into Client-1 as andrew-admin. System > remote desktop (RDP) > Select users > enter domain users > check names > ok this will now allow domain users to allow RDP to access the VM.

![Screenshot 2025-04-25 172550](https://github.com/user-attachments/assets/7fda1133-3ec3-4596-8045-a7840e5696f6)

Now we log back into Dc-1 and we will run this script: https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1 on powershell. to do this type powershell ISE and right click as an administrator. click new script and copy / paste the script . (bonus note) chagne the number of accountants from 10000 to 1000. Now run the script.

![Screenshot 2025-04-25 173351](https://github.com/user-attachments/assets/b4c6e1bf-c854-4f55-baa9-f079e94b9901)

We can now check to see if our accounts have been created by opening active directory users and computers under the _EMPLOYEES tab undner mydomain.com and should see all the accounts created.

![Screenshot 2025-04-25 173538](https://github.com/user-attachments/assets/73737207-a45d-423a-905f-6b709ca4b7b9)

Finally, once created, we can choose an employee and sign on to Client-1 using one of the accounts created. To confirm were logged in, run powershell and we should see our user logged into the VM. 

![Screenshot 2025-04-25 174121](https://github.com/user-attachments/assets/abb9341b-27dd-4129-9c11-9816ea592fc2)
