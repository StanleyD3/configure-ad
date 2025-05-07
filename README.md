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
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Create 2 virtual machines within the same network and resource group. One acting as the domain controller and the other as an end-user's computer
- Install active directory on the VM that will serve as your domain controller 
- Make your domain controller's IP address static and use that IP as the DNS server for end-user computers
- In Active Directory Users and Computers create Organizational Units to add, organize, and set permissions for the users being added.

<h2>Deployment and Configuration Steps</h2>

![image](https://github.com/user-attachments/assets/f86e2c5e-d47e-46e0-8538-0212ec1da1dc)

- Create a resource group and within it 2 virtual machines within the same network and region. 
- One VM will be acting as the domain controller using windows server 2022 Datacenter and the other as an end-user's computer in which I will use windows 10. 
- The picture above shows the Domain controller's vm setup, aim to use 2 VCPU's to ensure it has enough computing power. You will have to create a username and password then you are free to hit "review and create" to start running the VM's.

<img width="639" alt="DC connect" src="https://github.com/user-attachments/assets/cbb1ccff-2df1-4e68-b165-10460a75199c" />

- Now that we have our "computers" we need to login the Domain Controller to disable the firewalls so that we are able to test connectivity
- Log-in the domain controller through remote desktop protocol/remote desktop connection with the username and password you created when making the VM's
- We are here to disable the windows firewall so that we can test the connectivity from the end-user computer

![image](https://github.com/user-attachments/assets/4ad96ab2-4a3a-4fb6-936c-d3025185515d)
- Once logged in, right click the windows start menu located in the bottom left corner and click run which will allow you to run a program by name
- we will be running wf.msc for windows firewall
- once the firewall is open, click the "windows Defender Firewall Properties" and disable the firewalls by changing the firewall state to off in the 3 tabs highlighted in the above picture.

![image](https://github.com/user-attachments/assets/9de984cb-a3cf-442f-b231-67c611f1f075)

- Next we want to configure the Domain Controller's NIC privite IP address to be static, then set the End-user's DNS settings to the Domain Controllers private IP address

![powershell con DNS](https://github.com/user-attachments/assets/675be111-7918-4854-8ace-f8f9725ba927)

- Restart both VM's to make sure the changes apply
- Afterwards log into the End-user computer with the username and password created
- Once in the end user's computer open powershell and ping the Domain Controller using it's IP address, you shoud get packets back as above showing connectivity
- Then we can run "IPconfig/ all" to confirm the output for the DNS settings, which should show the Domain Controller's private IP as in the above picture

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />
