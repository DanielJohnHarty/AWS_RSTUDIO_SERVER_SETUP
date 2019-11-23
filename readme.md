# Serve and manage your own private RStudio.server instance using AWS

Tired of spending hours adjusting you R instance each time you change computers, clients, country or corporate policy?

Using AWS, you can spin up a free EC2 instance, install RStudio-server and access it from anywhere. The only requirements, an internet connection and browser.

Prerequisites

- [x] A credit card - free  AWS options but card required for **potential** billing anyway
- [x] [ASW Account]([https://aws.amazon.com/console/) - Create an account!
- [x] Command line interface (powershell, bash, terminal, cmd are the most common) to remote access your AWS instance.  ***These are usually installed by default on all operating systems*.**

#### Step by Step Overview With Time Estimates
 
1. ##Create your Virtual Private Cloud (VPC) ***[1 minute]***
2. Create a *subnet* in your VPC for your instance to reside in ***[1 minute]***
3. Give your VPC an internet gateway ***[1 minute]***
4. Associate your subnet with the VPC's internet gateway to make it a *public subnet* ***[1 minute]***
5. Configure your VPC's route table ***[1 minute]***
6. Create a *Security Group* to accept or reject requests to access your instance ***[1 minute]***
7. Create an EC2 *(Amazon Elastic Cloud Computer)* instance.  ***[5 minutes]***
8. Connect to it from your browser

As you can see, the process is fast (especially with a tutorial). However if it takes time, or something is confusing or doesn't work as expected, don't despair. 

AWS is a simplified wrapper around a massively complex system.  Just because the steps look easy, doesn't mean there isn't a world of complexity underneath it.

If you're curious to see what you can do outside of a tutorial, try investigating the [AWS training and certification]([https://www.aws.training/](https://www.aws.training/)) pages. There is a lot there but it's not urgent. Also get snacks and coffee first for an ideal study ambience.

## Step by step in depth

This image shows the basic overview of our very simple AWS infrastructure.
The VPC is the boundary of this particular cloud space. The availability zone refers to where the AWS hardware actually resides.
The subnet is a partitioned off part of the VPC which can be configured in a specific way for a specific purpose.

![SolutionStructure](https://github.com/DanielJohnHarty/AWS_RSTUDIO_SERVER_SETUP/blob/master/Images/SolutionStructure.png)

## Step by Step Overview With Time Estimates
 
### Create your Virtual Private Cloud (VPC) ***[1 minute]***

A fairly simple process here. Click on services in the top right of your browser and search for VPC. You will be redirected to a space where you can create and ange your VPCs. Explore the interface and find the button to create a new VPC. Follow the instructions onscreen and make sure you follow the CIDR block as shown in the above image,

### Create a *subnet* in your VPC for your instance to reside in ***[1 minute]***

If you look at the the navigation pane on the left of the screen, you'll find a link to manage your subnets.

Follow the link and create your subnet. Attach it to the VPC you just created, and give it the CIDR block as shown in the above image.

### Give your VPC an internet gateway ***[1 minute]***
Again on the left had panel, you should see a link to manage your *internet gateways*.  Follow it and create one. You don't need to configure an internet gateway itself much but you do need to configure *other things* to use your internet gateway.

### Configure your VPC's route table ***[1 minute]***
OK so this probably the most un-intuitive part of the AWS infrastructure to configure for someone new to networking. 

Routing is basically telling your AWS infrastructure what to when it receives a request or when a device from within it to access outside to the internet. You configure this using a combination of route tables and security groups.

Create a new route table from the left hand panel. Give it a recognizable name and select your new VPC. This route table will be used to define how requests are handled.

Now select the route table and configure as in the below image.
![routetable_routes](https://github.com/DanielJohnHarty/AWS_RSTUDIO_SERVER_SETUP/blob/master/Images/routetable_routes.png)


**Now click the Subnet associations tab and associate this routetable to the subnet you created in a previous step.**
The first row indicates that requests heading to the internal network are not to be impeded.
The second row indicates that requests from within the associated subnet to the internet are also not to be impeded.

*This is actually an almost completely open network and is quite unsecure. In proder to control it more, we proceed to create a security group which will be used to accept or **ignore** requests to our devices within the subnet*

### Create a _Security Group_ to accept or reject requests to access your instance _**[1 minute]**_

OK so you want to create a security group. Guess how you get there? From the panel on the left!

Select security groups. Create a new one, with a memorable name and associate it to your VPC. *A security group can be used all across your VPC, even if you have many subnets and applications!*

When your security group is created, update its inbound and outbound rules as the below images:

![security_group_inbound_rules](https://github.com/DanielJohnHarty/AWS_RSTUDIO_SERVER_SETUP/blob/master/Images/security_group_inbound_rules.png)

![security_group_outbound_rules](https://github.com/DanielJohnHarty/AWS_RSTUDIO_SERVER_SETUP/blob/master/Images/security_group_outbound_rules)

So why are we doing this?
In order to access the future servers and instances in your AWS infrastructure, you will need to prove who you are and that you are supposed to have access. 

During creation of a server/EC2 instance, you can stipulate which security groups should have access to your instances. When you generate an 'ssh key' (an incomprehensible, enrypted document) you will use it to demonstrate that you should have access. 

### Create an EC2 *(Amazon Elastic Cloud Computer)* instance.  ***[5 minutes]***

Finally the good stuff! 

We've actually done most of the set-up work too so this part is simple.

Click the 'Services' link at the top left of the screen and search for the EC2 service. Click it and press the **'Launch Instance'** button.

This tutorial has parts which are only compatible with the Ubuntu operating system, so on the first screen where you define the baseline of your new instance, choose Ubuntu:

![image_type](https://github.com/DanielJohnHarty/AWS_RSTUDIO_SERVER_SETUP/blob/master/Images/image_type.png)

On each screen, press the button on the bottom right which goes to the next step. There are many opportunities to press a button which will skip steps - avoid that! Make sure that you see every screen to avoid that something you don't want ends up in your final EC2 instance.

In the next screen, most of the form can be left as is, but you will need to specify the VPC, the subnet, ensure that automatic assignment of an elastic IP are set:

![instance_details](https://github.com/DanielJohnHarty/AWS_RSTUDIO_SERVER_SETUP/blob/master/Images/instance_details.png)

... and you also need to add a small script to the Advanced Options at the bottom of the page:

![instance_details2](https://github.com/DanielJohnHarty/AWS_RSTUDIO_SERVER_SETUP/blob/master/Images/instance_details2.png)

The full script is:

```
#!/bin/bash

# This updates the systems current packages
sudo apt-get update -y

# This upgrades the packages to the latest version
sudo apt-get upgrade -y

# These are core tools and libraries  rstudio-server depends on
sudo apt-get install r-base -y
sudo apt-get install gdebi-core -y

# Download rstudio-server to a specific directory...
mkdir /home/rstudio-server/download
cd /home/rstudio-server/download
wget https://download2.rstudio.org/server/bionic/amd64/rstudio-server-1.2.5019-amd64.deb

# ...and install it. Note that there is no '-y' parameter for this install so we pipe "y" to answer its install confirmation request
printf "y" | sudo gdebi rstudio-server-1.2.5019-amd64.deb

# Assign password to default ubuntu user. Initial space avoids it being stored in console command history
 echo -e "<yourpassword>\n<yourpassword>" | sudo passwd ubuntu
```
What does this script do?

1. It updates your system
2. It downloads the RStudio-Server package and installs it on your instance
3. It updates the password of the default user to whatever you decide to replace  **yourpassword** with. The last line of the script is indented by 1 space, that is intentional to avoid that it is recorded in the instance history.

On the security group step, make sure to associate the security group you created with the instance.

When you reach the final page and launch is the only option, go ahead and **Launch** it.

### Connect to it from your browser***[1 minute1]***

It will take a few minutes for your instance to launch and the startup stript to run but when it has, you should be able to see all of the details from within the EC2 instances dashboard:


![instance_description](https://github.com/DanielJohnHarty/AWS_RSTUDIO_SERVER_SETUP/blob/master/Images/instance_description.png)

Doubel check all the points in red here. If any are missing or not something we defined in a previous step, it could be that you've missed a step and need to go back and correct it. **The good news is that you can, even though the instance is created!**

Take a note of the public IP.

Go to your browser and browse to:

<yourEC2InstancePublicIP:8787>

![browsing_to_instance](https://github.com/DanielJohnHarty/AWS_RSTUDIO_SERVER_SETUP/blob/master/Images/browsing_to_instance.png)

*Note that the port 8787 is used by the RStudio-Server software as the standard port to receive RStudio requests on.

If all is well, you should see the login screen in the above image. USe the user 'Ubuntu' and the password you specified in the startup script and you should be in.

Enjoy!