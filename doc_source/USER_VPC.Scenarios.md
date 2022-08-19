# Scenarios for accessing a DB cluster in a VPC<a name="USER_VPC.Scenarios"></a>

Amazon Aurora supports the following scenarios for accessing a DB cluster in a VPC:
+ [An EC2 instance in the same VPC](#USER_VPC.Scenario1)
+ [An EC2 instance in a different VPC](#USER_VPC.Scenario3)
+ [A client application through the internet](#USER_VPC.Scenario4)
+ [A private network](#USER_VPC.NotPublic)

## A DB cluster in a VPC accessed by an EC2 instance in the same VPC<a name="USER_VPC.Scenario1"></a>

A common use of a DB cluster in a VPC is to share data with an application server that is running in an EC2 instance in the same VPC\.

The following diagram shows this scenario\.

![\[VPC scenario with a public web server and a private database\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/con-VPC-sec-grp-aurora.png)

The simplest way to manage access between EC2 instances and DB clusters in the same VPC is to do the following:
+ Create a VPC security group for your DB clusters to be in\. This security group can be used to restrict access to the DB clusters\. For example, you can create a custom rule for this security group\. This might allow TCP access using the port that you assigned to the DB cluster when you created it and an IP address you use to access the DB cluster for development or other purposes\.
+ Create a VPC security group for your EC2 instances \(web servers and clients\) to be in\. This security group can, if needed, allow access to the EC2 instance from the internet by using the VPC's routing table\. For example, you can set rules on this security group to allow TCP access to the EC2 instance over port 22\.
+ Create custom rules in the security group for your DB clusters that allow connections from the security group you created for your EC2 instances\. These rules might allow any member of the security group to access the DB clusters\.

There is an additional public and private subnet in a separate Availability Zone\. An RDS DB subnet group requires a subnet in at least two Availability Zones\. The additional subnet makes it easy to switch to a Multi\-AZ DB instance deployment in the future\.

For a tutorial that shows you how to create a VPC with both public and private subnets for this scenario, see [Tutorial: Create a VPC for use with a DB cluster \(IPv4 only\)](CHAP_Tutorials.WebServerDB.CreateVPC.md)\.

**To create a rule in a VPC security group that allows connections from another security group, do the following:**

1.  Sign in to the AWS Management Console and open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc](https://console.aws.amazon.com/vpc)\.

1.  In the navigation pane, choose **Security groups**\.

1. Choose or create a security group for which you want to allow access to members of another security group\. In the preceding scenario, this is the security group that you use for your DB clusters\. Choose the **Inbound rules** tab, and then choose **Edit inbound rules**\.

1. On the **Edit inbound rules** page, choose **Add rule**\.

1. For **Type**, choose the entry that corresponds to the port you used when you created your DB cluster, such as **MYSQL/Aurora**\.

1. In the **Source** box, start typing the ID of the security group, which lists the matching security groups\. Choose the security group with members that you want to have access to the resources protected by this security group\. In the scenario preceding, this is the security group that you use for your EC2 instance\.

1. If required, repeat the steps for the TCP protocol by creating a rule with **All TCP** as the **Type** and your security group in the **Source** box\. If you intend to use the UDP protocol, create a rule with **All UDP** as the **Type** and your security group in **Source**\.

1. Choose **Save rules**\.

The following screen shows an inbound rule with a security group for its source\.

![\[Adding a security group to another security group's rules\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/con-vpc-add-sg-rule.png)

## A DB cluster in a VPC accessed by an EC2 instance in a different VPC<a name="USER_VPC.Scenario3"></a>

When your DB clusters is in a different VPC from the EC2 instance you are using to access it, you can use VPC peering to access the DB cluster\.

The following diagram shows this scenario\. 

![\[A DB Instance in a VPC Accessed by an EC2 Instance in a Different VPC\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/RDSVPC2EC2VPC-aurora.png)

A VPC peering connection is a networking connection between two VPCs that enables you to route traffic between them using private IP addresses\. Resources in either VPC can communicate with each other as if they are within the same network\. You can create a VPC peering connection between your own VPCs, with a VPC in another AWS account, or with a VPC in a different AWS Region\. To learn more about VPC peering, see [VPC peering](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-peering.html) in the *Amazon Virtual Private Cloud User Guide*\.

## A DB cluster in a VPC accessed by a client application through the internet<a name="USER_VPC.Scenario4"></a>

To access a DB clusters in a VPC from a client application through the internet, you configure a VPC with a single public subnet, and an internet gateway to enable communication over the internet\.

The following diagram shows this scenario\.

![\[A DB cluster in a VPC Accessed by a client application through the internet\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/GS-VPC-network-aurora.png)

We recommend the following configuration:

 
+ A VPC of size /16 \(for example CIDR: 10\.0\.0\.0/16\)\. This size provides 65,536 private IP addresses\.
+ A subnet of size /24 \(for example CIDR: 10\.0\.0\.0/24\)\. This size provides 256 private IP addresses\.
+ An Amazon Aurora DB clusterthat is associated with the VPC and the subnet\. Amazon RDS assigns an IP address within the subnet to your DB cluster\.
+ An internet gateway which connects the VPC to the internet and to other AWS products\.
+ A security group associated with the DB cluster\. The security group's inbound rules allow your client application to access to your DB cluster\.

For information about creating a DB clusters in a VPC, see [Creating a DB cluster in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.InstanceInVPC)\.

## A DB cluster in a VPC accessed by a private network<a name="USER_VPC.NotPublic"></a>

If your DB cluster isn't publicly accessible, you have the following options for accessing it from a private network:
+ An AWS Site\-to\-Site VPN connection\. For more information, see [What is AWS Site\-to\-Site VPN?](https://docs.aws.amazon.com/vpn/latest/s2svpn/VPC_VPN.html)
+ An AWS Direct Connect connection\. For more information, see [What is AWS Direct Connect?](https://docs.aws.amazon.com/directconnect/latest/UserGuide/Welcome.html)

The following diagram shows a scenario with an AWS Site\-to\-Site VPN connection\. 

![\[A DB clusters in a VPC accessed by a private network.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/site-to-site-vpn-connection-aurora.png)

For more information, see [Internetwork traffic privacy](inter-network-traffic-privacy.md)\.