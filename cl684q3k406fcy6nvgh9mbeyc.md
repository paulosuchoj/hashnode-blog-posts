## Creating an Active Directory Environment in AWS Part 2: Configuring the Domain Controller and Installing Active Directory

# PREMISES

This guide will be divided in 3 parts: Setting Up the EC2 Instance, Configuring the Domain Controller and Installing Active Directory, and Populating the Domain.

This is the second part, and you can read the first one here: [**Creating an Active Directory Environment in AWS Part 1: Setting Up the EC2 Instance**](https://paulosuchoj.hashnode.dev/creating-an-active-directory-environment-in-aws-part-1-setting-up-the-ec2-instance)

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

# 1. SETTING UP A DOMAIN CONTROLLER FOR ACTIVE DIRECTORY

The domain controller (or DC for short) is a server that responds to authentication requests within a network domain. It authenticates users, computers, services accounts etc., as well as enforcing security policies for the domain. The domain controller is where our Active Directory instance will live, and it’s also like that in corporate environments, although a corporation can have multiple domain controllers for redundancy.

First, for simplicity and organization’s sake, we’ll rename the computer to DC01. This isn’t needed for a lab, but it’s good practice. After you connect to it through RDP, open the Server Manager. Click on “Configure this local server” on the dashboard, and then click on the “**Computer name**”

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659199368346/j_KG0-pEm.png align="left")

On the window that appears, click on Change. Then rename it to DC01 and click OK. Windows will prompt you to restart the server to apply changes. Do so and reconnect to the instance once it’s back online.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659199380283/NvhMYjsST.png align="left")

Now we must configure the DC instance with a static IP address. The first step is getting the IP address AWS assigned to the EC2 instance. Open a command prompt and type in the command ```ipconfig```. Then note the IPs on the **IPv4 Address**, **Subnet Mask** and **Default Gateway** lines under **Ethernet adapter Ethernet 2**.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659199515798/MZKpSOhcM.png align="left")

With IP addresses in hand, open the **Control Panel**, and go to **Network and Sharing Center**. Click on the network named as **Ethernet 2**. Click on Properties on the window that pops up, then find the **Internet Protocol Version 4 (TCP/IPv4)** item, and with it selected, click on **Properties**.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659199548330/C6_rKIfCr.png align="left")

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659199560726/EOpuUcM5B.png align="left")

Select the option “**Use the following IP address**” on the configurations, and then type in the three IP addresses we got from the command prompt in their respective fields. For the DNS you can just use whichever you see fit, Google’s is an easy one, just fill the fields in as shown below

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659199579698/36Hswmm1L.png align="left")

When you click on OK your instance will probably go down for a few seconds while configurations are changed. Simply wait for it to be back online.

# 2. INSTALLING THE ACTIVE DIRECTORY SERVICES

On the Server Manager dashboard, go to **Manage** on the top right menu, and then select **Add Roles and Features**. Select the server and then on the **Server Roles** add **Active Directory Domain Services** and **DNS Server** (click Add on both screens that show up when selecting each feature).

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659199631062/4DUT8SV5c.png align="left")

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659199634656/L9_nyfc3I.png align="left")

Confirm the selection and click on Install and wait for it to finish.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659199643840/_eGk1KmpC.png align="left")

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659199647328/UoPI-EK1p.png align="left")

When the installation is done, you’ll notice a warning sign next to a flag on the Server Manager dashboard. Click on it, and then on “**Promote this server to a domain controller**”

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659199660066/o3DTZ7jqy.png align="left")

On the Deployment Configuration screen, select “**Add a new forest**” and then define a **Root domain name** (it can be anything you want in a similar format to corp.labtestname.com). Windows will ask you to define a recovery password for the DC, do so. Then click Next until everything is finished.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659199683390/xXm4FNvgU.png align="left")

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1659199687298/m4ptqGAzT.png align="left")

With this, the Domain Controller is configured, and Active Directory and its related administrative services are installed on the instance. In the next, and final, part of the guide, we'll see how to populate the domain with different kinds of users, groups and even add another EC2 instance as a computer in our domain. 

You can read it here: 
