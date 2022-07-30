## Creating an Active Directory Environment in AWS Part 3: Populating the Domain

# PREMISES

This guide will be divided in 3 parts: Setting Up the EC2 Instance, Configuring the Domain Controller and Installing Active Directory, and Populating the Domain.

This is the third and final part, and you can read the other two here: 

1. [Creating an Active Directory Environment in AWS Part 1: Setting Up the EC2 Instance](https://paulosuchoj.hashnode.dev/creating-an-active-directory-environment-in-aws-part-1-setting-up-the-ec2-instance)
2. [Creating an Active Directory Environment in AWS Part 2: Configuring the Domain Controller and Installing Active Directory](https://paulosuchoj.hashnode.dev/creating-an-active-directory-environment-in-aws-part-2-configuring-the-domain-controller-and-installing-active-directory)

It also assumes you have the following:

- An AWS account
- Basic understanding of:
    - Using said AWS account and features
    - Windows and Windows Server
    - Command Prompt and/or PowerShell
    - IP addresses and networks
- A willing to learn and experiment

# PREFACE

This is a simple study showing how I’ve created my own EC2 Windows Server instance and configured an Active Directory test environment, so I could learn the process and then work with Azure to test a hybrid connection and synchronizing objects to my cloud tenant.

In no way regard this as an enterprise-level work, especially since I’m still learning both AWS and Azure and the environment built here isn’t compliant with security best-practices and guidelines expected from a corporate setup.

# 1. POPULATING THE DOMAIN

Now that our Active Directory is set up, we will populate it with objects: organizational units (OUs), users, groups, and computers. These are the basic things we need in a lab to manipulate and run tests with AD.

To add these all, first go to the Server Manager, go to **Tools**, and then click on **Active Directory Administrative Center**.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659200184578/QboW7co9B.png align="left")

## 1.1. Organizational Units (OU)

On the left side, you should see your AD forest. Right click on it, go to **New** and then click on **Organizational Units**. The OU should then appear on your domain

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659200223570/OPhWsK8qa.png align="left")

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659200245030/1Mu2pCWTX.png align="left")

You can add three basic OUs to this main one. We will go with Users, Groups and Computers. On the right-hand menu, right click on the OU, go to **New** and then **Organizational Unit**. Create the three OUs using the same steps.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659200264381/V7F7zCgME.png align="left")

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659200300363/Nq7x7U_83.png align="left")

You can open Active Directory Users and Computers, and see that the OUs we have created are now there:

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659200308837/cNRG5Nqzh.png align="left")

## 1.2. Adding Users (Regular Users, Service Accounts, Server Admin, Workstation Admin)

With AD open, we can now create users through there. Right click on the Users OU, select New and then User.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659200332279/x9RP0eT1O.png align="left")

Fill in the data for the user (**First Name**, **Last Name**, and a **User logon name**). Give it a password (you can define it so that the password never expires, since this is just a lab), and then click on Finish.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659200356351/KxBLxqZcG.png align="left")

A reminder that setting a password to never expire is ***NOT*** a best practice in production environments, except in very specific cases.

This first user is the Domain Admin, so add him to the respective group. Go to Users, open the profile up and go to the Member Of tab. Click on Add, type in Domain Admins, click on OK and then Apply and close it.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659200390684/IZ5YtFqJ5.png align="left")

Let us create two other regular users using another method, through the Server Manager and the AD Admin Center. Go to the OU, then on the right-hand menu, go to **Users** and **New**, then User again.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659200405009/4hqLOwd-e.png align="left")

Fill in the same basic data we filled on AD, and then click on OK. Create another user, so we have two. Check if they have already been added to the Domain Users group, by checking on the Member Of tab. AD services should do that automatically (if it hasn’t, just add both users to Domain Users group, like we did for the first user in Domain Admins).

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659200413991/cQq5s3atg6.png align="left")

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659200417349/Pumfi9dWk.png align="left")

To finish it up, create two more users, a server admin, and a workstation admin. To help organize everything, you can set the Description of each user to show what they are:

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659200429162/0i0jRKUxp.png align="left")

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659200433021/9HXMiZ_bd.png align="left")

This should give us a total of 6 accounts on the domain:

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659200440760/fYH6pretf.png align="left")

## 1.3. Creating Groups

On the AD Administrative Center, go to the OU, select **Groups**. Then, on the right-hand menu, click on **New**, and then **Group**. We can create groups for both Server and Workstation admins.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659200476242/baieR8Cjr.png align="left")

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659200478978/4pwcZa-92.png align="left")

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659200482456/xGb5L7gPp.png align="left")

Then add our admins to their respective groups

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659200489020/MI21estAM.png align="left")

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659200492440/B9b0tNOf4.png align="left")

## 1.4. Adding Computers to the Domain

The computers we will add to our test domain are other instances created on AWS, copied from the original one that we turned into DC01. This can be done quick and easy by cloning the EC2 instance.

Go to the EC2 Dashboard, then to **Instances**. Select the original instances, then on the **Actions** dropdown menu, go to **Images and templates**, and click on “**Launch more like this**”

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659200532545/lPeybH2Qt.png align="left")

Just create a new key pair for this one, like we did for the original instance, and then launch it.

For this new computer to be added to our domain, we need to define its primary DNS as the fixed IP we configured on DC01. So, connect to DC01, get the IP address, and then connect to the new instance.

Go to the **Control Panel**, then **Network and Sharing Center**. Click on the Ethernet adapter, then on the status dialog, click **Properties**. On the properties, like before, find the IPv4 line, and then click on **Properties**. Check the “**Use the following DNS server addresses**”, and set up the DC01 IP on the “**Preferred DNS server**” field:

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659200572984/K03sf-2NR.png align="left")

After that, we will rename the instance to something like SRV01, and add it to the domain:

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659200587738/Xw8-HeEa-.png align="left")

When you click OK, Windows will prompt you to enter a user’s credentials to authenticate into the domain. Pick any of the users created previously and type in their ID and password. If done correctly, you’ll get a dialog with the confirmation, welcoming you to the domain.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659200599654/rXqy6cCSX.png align="left")

As before when we changed the server’s name to DC01, you’ll need to restart your instance for the changes to take effect. You can almost immediately see that the new server has been added to your domain on DC01. Windows puts them by default on the standard Computers OU directly under the forest. You can just move it to the Computers OU we created:

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659200635614/1LsIsAJTM.png align="left")

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659200670075/9T-coMGob.png align="left")

This concludes the guide. You can add more computers, users, and groups and play around with them on the domain. 

For next steps, you could try creating some Group Policies with the objects we already have, to effect changes within Active Directory, such as adding the Server_Admins group to the Administrators of all server machines and so on.

I hope this was helpful in some way since it was just me following other tutorials and putting my own spin on it to have a hands-on experience with AWS and Active Directory.

# CREDITS AND REFERENCES

Big thanks to the sources I’ve used listed below, we always stand tall on the shoulders of giants.

1. [Appendix B Setting Up the Test Environment | Microsoft Docs](https://docs.microsoft.com/en-us/windows-server/identity/solution-guides/appendix-b--setting-up-the-test-environment)
2. [Creating an Active Directory Lab in AWS – Fat Rodzianko](https://fatrodzianko.com/2019/08/05/creating-an-active-directory-lab-in-aws/)
3. [Active Directory Glossary - Terms and Fundamental Concepts - Active Directory Pro](https://activedirectorypro.com/glossary/)

