# Host Your Own Instance of RStudio on AWS in *11 Minutes*, and Access it From *Anywhere* Through Your Web Browser

Are you tired of spending hours configuring RStudio each time you change computers, clients, country or corporate policy?

Using AWS, you can spin up a free EC2 instance, install RStudio-server on it and access it from anywhere using an internet browser. In 11 minutes or less!*

Requirements:

- [x] A credit card - this tutorial uses free AWS services but creating an AWS account requires a payment method for **potential** future billing
- [x] [An ASW Account](https://aws.amazon.com/console/) - Go ahead and create one!
- [x] A command line interface tool (powershell, bash, terminal, cmd are the most common) to remote access your AWS instance.  ***These are usually installed by default on all operating systems*.**

#### What you'll be doing:
 
1. Creating your Virtual Private Cloud (VPC) ***[1 minute]***
2. Creating a *subnet* in your VPC for your RStudio instance to reside in ***[1 minute]***
3. Giving your VPC an internet gateway ***[1 minute]***
4. Associating your subnet to the VPC's internet gateway to make it a *public subnet* with internet access ***[1 minute]***
5. Creating and configuring your VPC's route table ***[1 minute]***
6. Creating a *Security Group* to allow or ignore requests to access your RStudio instance ***[1 minute]***
7. Creating an EC2 instance *(Amazon Elastic Cloud Computer)* running your RStudio-service.  ***[5 minutes]***
8. Connecting to your RStudio from your internet browser ***[30 seconds]***

As you can see, the process is fast (especially with a tutorial). However if it takes time, or something doesn't work as expected, double check the steps and describe the problem to Google - maybe it has an answer. 

AWS is a simplified wrapper around a massively complex system.  Just because the steps look easy, doesn't mean there isn't a world of complexity underneath it.

If you're curious to see what you can do outside of a tutorial, try investigating the [AWS training and certification]([https://www.aws.training/](https://www.aws.training/)) pages. There is a lot there but it's not urgent. 

Snacks and coffee on hand make for an ideal study environment.

## Step by step in depth

This image shows a basic overview of a very simple AWS infrastructure.
The VPC (Virtual Private Cloud) is the boundary of our AWS cloud space. It's created on physical hardware in a physical region of the globe, identified by the region. A region has multiple, independent availability zones which are connected in a high velocity network. There are multiple availability zones in each region so that failures in one can be backed up by others in the region.

The subnet is a partitioned of part of the VPC which servers and databases reside in.

![SolutionStructure](https://github.com/DanielJohnHarty/AWS_RSTUDIO_SERVER_SETUP/blob/master/Images/SolutionStructure.png)

## Step by Step (Walkthrough)
 
 The following steps require you to be logged in to your AWS console environment. 
 
 Before you start going through the steps, take  a moment to familiarize yourself with the AWS console interface. Different services can be found by clicking the services button in the top left of the screen, and then shortcuts on the left hand side will take you to the necessary pages and forms to manage the selected service.
 
### Create your Virtual Private Cloud (VPC) ***[1 minute]***

This is a fairly simple process. Click on services in the top right of your browser and search for VPC. You will be redirected to a space where you can create and manange your VPCs. Explore the interface and find the button to create a new VPC. Follow the instructions onscreen and set CIDR block to **10.0.0.0/16**.

### Create a *subnet* in your VPC for your instance to reside in ***[1 minute]***

If you look at the the navigation pane on the left of the screen, you'll find a link to manage your subnets.

Follow the link and create a subnet. Attach it to the VPC you just created, and set the CIDR block to **10.0.0.0/20**.

>The **CIDR block** parameter specifies what IP addresses are available to be assigned within a VPC and it's subnets.
>The CIDR block values here allow up to 65536 different IP addresses to exist within your VPC and up to  4096 within your subnet. That leaves up to 61440 which you can assign to future subnets you create. For this tutorial though, we only need 1. 

### Give your VPC an internet gateway ***[1 minute]***
Again on the left had panel, you should see a link to manage your *internet gateways*.  Follow it and create one. You don't need to configure an internet gateway itself much but you do need to configure *other things* to use your internet gateway.

### Create/Configure your VPC's route table ***[1 minute]***
For those new to networking, this probably the most unintuitive part of configuration of AWS infrastructure.

Routing is basically telling your AWS infrastructure what to when it receives a request from outside your VPC, or conversely when a device from within your VPC attempts to access the the internet. You configure this using a combination of *route tables* and *security groups*.

Create a new route table from the left hand panel. Give it a recognizable name and select your new VPC. This route table will be used to define how requests are handled.

Now select the route table and configure as in the below image.
![routetable_routes](https://github.com/DanielJohnHarty/AWS_RSTUDIO_SERVER_SETUP/blob/master/Images/routetable_routes.png)


**Now click the Subnet associations tab and associate this routetable to the subnet you created in a previous step.**
The first row indicates that requests heading to the internal network are not to be impeded.
The second row indicates that requests from within the associated subnet to the internet are also not to be impeded.

*This is actually an almost completely open network and is quite unsecure. In order to control it more, we proceed to create a security group. 

### Create a *Security Group* to accept or ignore requests to access your RStudio **[1 minute]**

OK so you want to create a security group. Guess how you get there? From the panel on the left!

Select security groups. Create a new one, with a memorable name and associate it to your VPC. *A security group can be used all across your VPC, even if you have many subnets and applications!*

When your security group is created, update its inbound and outbound rules as the below images:

![security_group_inbound_rules](https://github.com/DanielJohnHarty/AWS_RSTUDIO_SERVER_SETUP/blob/master/Images/security_group_inbound_rules.png)

![security_group_outbound_rules](https://github.com/DanielJohnHarty/AWS_RSTUDIO_SERVER_SETUP/blob/master/Images/security_group_outbound_rules.png)

***What's a security group for?***

Security groups act as firewalls for your AWS instances. Their configuration defines what kind of requests are permitted access to the device they're protecting. Configuration comes down to request protocol, source ip address and the port they are received on. e.g. We could configure a security group to only accept ssh requests on port 22 from a specific PC in your house. The opposite end of the scale would be allowing all traffic from anywhere.

### Create your EC2 *(Amazon Elastic Cloud Computer)* running your RStudio-service instance ***[5 minutes]***

Finally the good stuff! 

We've actually done most of the set-up work so this part is simple.

Click the 'Services' link at the top left of the screen and search for the EC2 service. Click it and press the **'Launch Instance'** button.

This tutorial has parts has been tested using the Ubuntu operating system, so on the first screen where you define the baseline of your new instance, choose Ubuntu 18.04:

![image_type](https://github.com/DanielJohnHarty/AWS_RSTUDIO_SERVER_SETUP/blob/master/Images/image_type.png)

> The following screens can passed one by one but can too easily be skipped, instead launching directly the instance with defulat values. The danger default values is that you can't be sure if your instance will be as cheap or as performant as you'd like. For this tutorial, default values will not work - so avoid skipping any pages for now.

In the next screen *(Configure Instance Details)*, most of the form can be left as is, but you will need to specify the VPC, the subnet, ensure that automatic assignment of an elastic IP are set:

![instance_details](https://github.com/DanielJohnHarty/AWS_RSTUDIO_SERVER_SETUP/blob/master/Images/instance_details.png)

... and you also need to adapt a small script (changing <yourpassword> for a password of your choice. Once adapted, after opening up the Advanced Options at the bottom of the page, you can copy paste the script in to the part of the form titles 'User Details':

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
3. It updates the password of the default user to whatever you decide to replace  **<yourpassword>** with. The last line of the script is indented by a space - **that is intentional to avoid that it is recorded in the instance history**.

On the security group step, make sure to associate the security group you created with the instance.

When you reach the final page and launch is the only option, go ahead and **Launch** it.

### Connect to your RStudio from your internet browser ***[30 seconds]***

It will take a few minutes for your instance to launch and the startup stript to run but when it has, you should be able to see all of the details from within the EC2 instances dashboard:


![instance_description](https://github.com/DanielJohnHarty/AWS_RSTUDIO_SERVER_SETUP/blob/master/Images/instance_description.png)

Double check all the points in red here. If any are missing or not something we defined in a previous step, it could be that you've missed a step and need to go back and correct it. **The good news is that you can, even though the instance is created!**

Take a note of the public IP. If it is absent, you can assign a public IP from the left hand panel, 'Elastic IPs'. Allocate a public IP then associate it to your instance:

![AssociateIPAddress](https://github.com/DanielJohnHarty/AWS_RSTUDIO_SERVER_SETUP/blob/master/Images/AssociateIPAddress.png)



### ...and finally:

Go to your browser and browse to:

<yourEC2InstancePublicIP:8787>

![browsing_to_instance](https://github.com/DanielJohnHarty/AWS_RSTUDIO_SERVER_SETUP/blob/master/Images/browsing_to_instance.png)

*Note that the port 8787 is used by the RStudio-Server software as the standard port to receive RStudio requests on.

If all is well, you should see the login screen in the above image. USe the user 'Ubuntu' and the password you specified in the startup script and you should be in.

Enjoy!