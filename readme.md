# Serve and manage your own private RStudio.server instance using AWS

Tired of spending hours adjusting you R instance each time you change computers, clients, country or corporate policy?

Using AWS, you can spin up a free EC2 instance, install RStudio-server and access it from anywhere. The only requirements, an internet connection and browser.

Prerequisites

- [x] A credit card - free  AWS options but card required for **potential** billing anyway
- [x] [ASW Account]([https://aws.amazon.com/console/) - Create an account!
- [x] Command line interface (powershell, bash, terminal, cmd are the most common) to remote access your AWS instance.  ***These are usually installed by default on all operating systems*.**

#### Step by Step Overview With Time Estimates
 
1. Create your Virtual Private Cloud (VPC) ***[1 minute]***
2. Create a *subnet* in your VPC for your instance to reside in ***[1 minute]***
3. Give your VPC an internet gateway ***[1 minute]***
4. Associate your subnet with the VPC's internet gateway to make it a *public subnet* ***[1 minute]***
5. Configure your VPC's route table ***[1 minute]***
6. Create a *Security Group* to accept or reject requests to access your instance ***[1 minute]***
7. Create an EC2 *(Amazon Elastic Cloud Computer)* instance.  ***[5 minutes]***

As you can see, the process is fast (especially with a tutorial). However if it takes time, or something is confusing or doesn't work as expected, don't despair. 

AWS is a simplified wrapper around a massively complex system.  Just because the steps look easy, doesn't mean there isn't a world of complexity underneath it.

If you're curious to see what you can do outside of a tutorial, try investigating the [AWS training and certification]([https://www.aws.training/](https://www.aws.training/)) pages. There is a lot there but it's not urgent. Also get snacks and coffee first for an ideal study ambience.

## Step by step in depth

This image shows the basic overview of our very simple AWS infrastructure.
The VPC is the boundary of this particular cloud space. The availability zone refers to where the AWS hardware actually resides.
The subnet is a partitioned off part of the VPC which can be configured in a specific way for a specific purpose.

![InfrastructureOverviewWithCidrBlock](https://github.com/DanielJohnHarty/AWS_RSTUDIO_SERVER_SETUP/blob/master/Images/SolutionStructure.png)
![browsing_to_instance](https://github.com/DanielJohnHarty/AWS_RSTUDIO_SERVER_SETUP/blob/master/Images/browsing_to_instance.png)

![connecting_to_instance](https://github.com/DanielJohnHarty/AWS_RSTUDIO_SERVER_SETUP/blob/master/Images/connecting_to_instance.png)

![image_type](https://github.com/DanielJohnHarty/AWS_RSTUDIO_SERVER_SETUP/blob/master/Images/image_type.png)

![instance_description](https://github.com/DanielJohnHarty/AWS_RSTUDIO_SERVER_SETUP/blob/master/Images/instance_description.png)

![instance_details](https://github.com/DanielJohnHarty/AWS_RSTUDIO_SERVER_SETUP/blob/master/Images/instance_details.png)

![instance_details2](https://github.com/DanielJohnHarty/AWS_RSTUDIO_SERVER_SETUP/blob/master/Images/instance_details2.png)

![routetable_routes](https://github.com/DanielJohnHarty/AWS_RSTUDIO_SERVER_SETUP/blob/master/Images/routetable_routes.png)

![routetable_subnet_association](https://github.com/DanielJohnHarty/AWS_RSTUDIO_SERVER_SETUP/blob/master/Images/routetable_subnet_association.png)

![security_group_inbound_rules](https://github.com/DanielJohnHarty/AWS_RSTUDIO_SERVER_SETUP/blob/master/Images/security_group_inbound_rules.png)

![security_group_outbound_rules](https://github.com/DanielJohnHarty/AWS_RSTUDIO_SERVER_SETUP/blob/master/Images/security_group_outbound_rules.png)

![SolutionStructure](https://github.com/DanielJohnHarty/AWS_RSTUDIO_SERVER_SETUP/blob/master/Images/SolutionStructure.png)


