## Creating an Active Directory Environment in AWS Part 1: Setting Up the EC2 Instance

# PREMISES

This guide will be divided in 3 parts: Setting Up the EC2 Instance, Configuring the Domain Controller and Installing Active Directory, and Populating the Domain.

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

# 1. SETTING UP AN AWS EC2 WINDOWS SERVER INSTANCE

First, we will create an EC2 instance on AWS with a free tier Windows Server version. On AWS start screen, search for **EC2** and it’ll show up as the first service:

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659197486111/9Ru1OifNO.png align="left")

Click on it and you’ll be taken to the EC2 dashboard. There click on **Launch Instance** in the middle-left of the dashboard:

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659197515646/N-F_XT6eA.png align="left")

On the instance settings screen that you’ll be taken to, first you can give a name to your EC2 instance if you so wish, such as Windows Server 2012 Host. Then, on the OS selection carrousel, click on **Windows**, and on the dropdown that shows up, you’ll select **Microsoft Windows Server 2012 R2 Base** (for the purposes of this lab):

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659197538777/GIBlEMQI4.png align="left")

On the **Key pair (login)** section, click on **Create new key pair**:

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659197559128/foPuQ6kv2.png align="left")

Then, on the dialogue that opens, give it a name, and select if you’ll use it with either OpenSSH or PuTTY. Since I’m using a regular RDC on my Windows PC, I chose the “pem” extension for OpenSSH.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659197572938/K4t8k8bYI.png align="left")

Be sure to save this key pair on a safe folder where you won’t lose it, since you need it for security matters regarding the EC2 instance you’re creating, such as getting the password for a remote connection.

In the next section, Network, be sure to select the option “**Allow RDP traffic from**”, and then change the dropdown to “**My IP**”. This at least ensures your instance will only accept remote connections from your current IP.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659197593515/AlPslBWCk.png align="left")

On **Configure storage** you can set up the size of storage however you like. Since I’m using AWS’ free tier, I’ll leave it as the standard 30GB offered.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659197601677/RXVD1sp38.png align="left")

When you’ve configured everything, check out the **Summary** to the side, and if you’re satisfied, click on Launch Instance

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659197612181/t1PrBwtuw.png align="left")

This will take you the Instances page. Wait until the instance states changes to Running, and then you’re good to connect to your EC2 Windows Server

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659197623935/bqteSa6K9.png align="left")

# 2. CONNECTING TO THE WINDOWS SERVER INSTANCE

When your instance is ready, click the checkbox beside it and then go to the **Actions** dropdown. Select **Connect**.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659198112999/fuhVKFjc3.png align="left")

On the next screen, change tabs to the “**RDP client**” tab. Click on the “**Download Remote desktop file**” button and AWS will generate a personalized RDP file you can use to get directly to the instance. Before you do that, however, you need the password. Click on the “**Get password” link**.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659198163850/2mqkzhCuT.png align="left")

You’ll be taken to the **Get Windows password** screen, where you’ll need to use the private key pair file we created earlier when setting up the instance to reveal your admin password. **Browse** to where you saved the keys (or paste their content in the text field), and then click on **Decrypt password**.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659198186318/Vwr2kfRzjp.png align="left")

The password will then be revealed in the previous screen, where you can copy it to a vault or just use it on the remote desktop connection

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659198197562/BqcKndnbH.png align="left")

Finally, to open a connection to your instance, simply run the RDP connection AWS generated for you (it should be in your Downloads folder), and type in or paste in the password you just got. If Windows shows you the warning dialogues, just click on “**Connect**” and then “**Yes**”:

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659198217107/D7ZvIV9hJ.png align="left")

And then you should be connected and logged in to your Windows Server

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659198273104/u5j0NO-v1.png align="left")

# 3. ADDING SECURITY GROUPS TO THE INSTANCES

Security groups are necessary so that the different instances in the environment can communicate with one another over the network. They act as firewalls on an instance, controlling incoming and outgoing traffic.

Before you start creating the groups, go to the left side menu, and under **Virtual private cloud**, go to **Subnets**. There, you’ll find the subnet for the instance you’ve created, and the IP address we can use to configure traffic in our group, under IPv4 CIDR:

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659198308066/V8ScZr5F1.png align="left")

Also note the name of the VPC, you’ll select it ahead to create the group.

On the EC2 console dashboard, go to the menu on the left, and under **Network & Security**, find **Security Groups**. Then, on the Security Groups screen, click on “**Create security group**”

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659198370357/pauTHpl-U.png align="left")

In the group creation screen, you’ll define a name, description and VPC under **Basic details**:

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659198388926/5YaZqwFNn.png align="left")

On the **Inbound rules**, select **Type** as “**All traffic**”, and on **Source** input the subnet address we got previously, which usually ends on “/20”. Just leave **Outbound rules** as it is.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659198426365/9ZyEuCZCN.png align="left")

Now, to configure the group on the instance, go to the dashboard, **Instances**, and select the instance that was created. On the **Actions** dropdown, go to **Security**, and then “**Change security groups**”

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659198451386/0UCYUjG6G.png align="left")

Then, on the **Change security** groups panel, find the group that was just created under the **Associated security groups** section, select it, click on “**Add security group**” and then **Save**

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659198473721/Y7BtnUIEy.png align="left")

This concludes the basic setup of the EC2 instance and general environment needs.

The guide continues to the setup of the Domain Controller and installation of the Active Directory services on the server, and you can read it here: [Creating an Active Directory Environment in AWS Part 2: Configuring the Domain Controller and Installing Active Directory](https://paulosuchoj.hashnode.dev/creating-an-active-directory-environment-in-aws-part-2-configuring-the-domain-controller-and-installing-active-directory)
