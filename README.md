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

- Exit the RDP connection and reopen microsoft azure once more
- Within Azure we want to configure the Domain Controller's NIC privite IP address to be static, for a consistent IP address
- Once it is static set the End-user's DNS settings to the Domain Controller's private IP address, so the end user can join the domain of our domain controller

![powershell con DNS](https://github.com/user-attachments/assets/675be111-7918-4854-8ace-f8f9725ba927)

- Restart the domain controller VM to make sure the changes apply
- Afterwards log into the End-user computer with the username and password created
- Once in the end user's computer open powershell and ping the Domain Controller using it's IP address, you shoud get packets back as above showing connectivity
- Then we can run "IPconfig/ all" to confirm the output for the DNS settings, which should show the Domain Controller's private IP as in the above picture
- Once confirmed, the setting has been set and it is time to install and deploy active directory

![image](https://github.com/user-attachments/assets/ea88dbf7-7aeb-4f09-8d45-17614920eae5)

- Log back into the Domain Controller and open service manager within the start menu
- Open add roles and features. Click next until you get to server roles

![image](https://github.com/user-attachments/assets/f0320abb-9ec3-4207-8fd8-88345106947e)

- under server roles click "actve directory DOmain services", a pop up will come up with the option to "add features". Click the "add features" button, then continue to click the next button until you get to the confrimation section and are able to click install.
- -after the install is complete you may click close

![promote](https://github.com/user-attachments/assets/c4eb48c1-9ec9-44fe-a889-f25bc2bf8c5f)

- Now you will see a grey flag in the upper right hand corner with a yellow triangle next to it. Click it
- Once clicked there should be an option to promote the server to a domain controller, click that as well

![image](https://github.com/user-attachments/assets/41b6939d-4fa2-47e5-83c6-cef2755d3842)

- Promoting the server will redirect you to the configuration of the active directory domin services
- click add a new forest, and nopw you can name your domain in this case it's "Mydomain.com"
- Next you will set your restore mode password, this will be used to repair and/or restore the Active directory's database

![image](https://github.com/user-attachments/assets/d414ec8d-6e0e-43a7-84f1-9fe032274df5)

- Next, you will uncheck the option to create DNS delegation
- Click nedt until you make it to the prerequisites page
- Here you will install the active directory domain services
- Once installed, the VM will automatically restart
- afterwards sign-in again, but this time add your domain name and a backslash before your original username. This way you are able to specify that your logging in as a domain user

![image](https://github.com/user-attachments/assets/9c515d95-6d2d-4477-8f65-b481d810d7d6)

- Once back in the Domain controller, go to the start menu and under windows Adminstative tools open "Active Directory Users and Computers"
- Right click the my domain.com file, then under new click Organizational Unit(OU) and name it as you please. I will make 2, 1 called ADMIN and another called EMPLOYEES. However in order to make it truly an admin we must users to the domain admin securty group

![image](https://github.com/user-attachments/assets/bbad8b33-7ad6-4483-aa23-b94750007f80)

- First we need to add a user into the ADMIN OU we created
- Open the ADMIN OU and right click inside, under new click on user and fill in the user's requested information as seen in the image above
- then hit next, fill in the password and set the password's settings for the user

![add as admin](https://github.com/user-attachments/assets/5e91144f-644a-4490-b171-5c03408a64db)

- Now to make the user officially an admin right click the user and open properties
- in properties go to "Member Of", click add, type in doamin admins, click check names, and lastly hit okay to apply the changes
- now logout and back in with the 

![image](https://github.com/user-attachments/assets/0ce9b6b4-e13b-41ed-a530-49bf78c38843)

Now 

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>
<br />
