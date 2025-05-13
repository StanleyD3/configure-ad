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
- Connect an enduser computer to the Domain of the Domain controller serving as the active directory
- Create Organizational Units to add, organize, and set permissions for the users being added.
- Practical use cases to managing and monitoring active directory

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
- click add a new forest, and now you can name your domain in this case it's "Mydomain.com"
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
- now the Active Directory has been configured and has at least 1 admin user

![image](https://github.com/user-attachments/assets/0ce9b6b4-e13b-41ed-a530-49bf78c38843)

- Now log-in to the end-user computer once more to add it to the domain
- once logged in right click the windows menu and open system
- in the right hand corner it will say "rename this PC (advanced)" which will open system properties, as shown in images above
- Here under computer name you will see an option that says "change" which will allow us to join our domain
- inside the change option under memerb of will be the option to switch from the assigned workgroup to a domain, after clicking the domain option type in your domain name and click "OK"

![add domain to client](https://github.com/user-attachments/assets/bdd8fa08-8e26-4f10-baf5-e97c0b360e67)

- After clicking OK, you will be prompted to log into the domain with an account with permission to join. IN this case that is the one Admin account created as shown in the above image
- You will now have logged in the end-user computer with the admin account created on our Domain Controller

![rdp access](https://github.com/user-attachments/assets/bf3b5ef3-c5b7-478e-91ce-c1a1f4c5cf13)

- Now that we're logged in lets make it so all users that we create can remotely sign in via the end-user computer
- open systems and go to remote desktop
- Next, under user accounts click "select users that can remotely access this PC"
- Then hit add and in the empty text box type in domain users, click check names, and click okay.
- Now it is official, any account registered in the domain controller can sign in via the end-user computer

![image](https://github.com/user-attachments/assets/59df3f6d-602a-4b37-afaa-80b5b3af75e1)

- Now let's create more users by making several accounts in one go
- open Windows PowerShell ISE, make sure to run as an administrator
- hit file and save as to create a file for the users we will create, and save.

![image](https://github.com/user-attachments/assets/41791136-1785-4a35-b2b3-08fc71b8eac8)

- Here i ran a script to create 10,000 users all under the same password, "Password1".
- once the script is created hit the green triangle on the top toolbar to run the script an it will create those 10,000 account, any red means there was an error
- Highlighted in the images above will show the OU I chose these accounts to get created in which is the EMPLOYEE folder created earlier
- you can check on these accounts by going back in "Active Directory Users and Computers" and opening the OU, ours being EMPLOYEE

  ![image](https://github.com/user-attachments/assets/b9e63de8-2c0e-43c9-b058-2573b499757c)

- Now that our users are created lets log into one of those accounts to confirm
- Pick an accunt and log in to the end-user computer with their credentials as in the image above via RDP, using Remote Desktop Connection
- you can check all past users that logged in this computer going to local disk (C:) and opening the users file as seen above. Any account that logs in will be saved here

![image](https://github.com/user-attachments/assets/ae21afd5-746c-481a-8371-c876ad8797a8)

- As far as managing accounts, 2 of the most common things will be password assistance and monitoring/tracing account activity
- In the Domain contrller within group policy management when you go into the domain's folder you can add group policies to implement mass changes
- you can edit the group policy by clicking edit, under computer configuration drop down security settings and then account policies.
- There will be several options but focusing on passwords, the most common being lockouts and in account lockout policies you may change the duration, waiting period, threshhold, and more.
- You may also use event viewr to monitor security logs as displayed in the image on the right. In event viewer open the windows log and click security to see all account activity like sign ins and failed attempts.

This concludes our comprehensive breakdown on configuring, deploying, and managing active directory. Thanks for your time and any questions you may reach out via linked in
https://www.linkedin.com/in/stanley-dansby-b77247175/
