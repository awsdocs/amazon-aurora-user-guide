# Connecting an EC2 instance and an Aurora DB cluster automatically<a name="ec2-rds-connect"></a>

You can use the RDS console to simplify setting up a connection between an EC2 instance and an Aurora DB cluster\. For instructions about setting up a connection between an EC2 instance and a Multi\-AZ DB cluster, see [](USER_VPC.Scenarios.md)\.

![\[Automatically connect an Aurora DB cluster with an EC2 instance.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/auto-connect-aurora-ec2.png)

If you want to connect to an EC2 instance that isn't in the same VPC as the Aurora DB cluster, see the scenarios in [Scenarios for accessing a DB cluster in a VPC](USER_VPC.Scenarios.md)\.

**Topics**
+ [Overview of automatic connectivity with an EC2 instance](#ec2-rds-connect-overview)
+ [Connecting an EC2 instance and an Aurora DB cluster automatically](#ec2-rds-connect-connecting)
+ [Viewing connected compute resources](#ec2-rds-connect-viewing)

## Overview of automatic connectivity with an EC2 instance<a name="ec2-rds-connect-overview"></a>

When you set up a connection between an EC2 instance and an Aurora DB cluster automatically, RDS configures the VPC security group for your EC2 instance and for your DB cluster\.

The following are requirements for connecting an EC2 instance with an Aurora DB cluster:
+ The EC2 instance must exist in the same VPC as the DB cluster\.

  If no EC2 instances exist in the same VPC, the console provides a link to create one\.
+ Currently, the DB cluster can't be an Aurora Serverless DB cluster or part of an Aurora global database\.
+ The user who is setting up connectivity must have permissions to perform the following EC2 operations:
  + `ec2:AuthorizeSecurityGroupEgress` 
  + `ec2:AuthorizeSecurityGroupIngress` 
  + `ec2:CreateSecurityGroup` 
  + `ec2:DescribeInstances` 
  + `ec2:DescribeNetworkInterfaces` 
  + `ec2:DescribeSecurityGroups` 
  + `ec2:ModifyNetworkInterfaceAttribute` 
  + `ec2:RevokeSecurityGroupEgress` 

If the DB instance and EC2 instance are in different Availability Zones, there is the possibility of cross Availability Zone costs\.

When you set up a connection to an EC2 instance, RDS takes an action based on the current configuration of the security groups associated with the DB cluster and EC2 instance, as described in the following table\.


****  

| Current RDS security group configuration | Current EC2 security group configuration | RDS action | 
| --- | --- | --- | 
|  There are one or more security groups associated with the DB cluster with a name that matches the pattern `rds-ec2-n` \(where `n` is a number\)\. A security group that matches the pattern hasn't been modified\. This security group has only one inbound rule with the VPC security group of the EC2 instance as the source\.  |  There are one or more security groups associated with the EC2 instance with a name that matches the pattern `rds-ec2-n` \(where `n` is a number\)\. A security group that matches the pattern hasn't been modified\. This security group has only one outbound rule with the VPC security group of the DB cluster as the source\.  |  RDS takes no action\. A connection was already configured automatically between the EC2 instance and DB cluster\. Because a connection already exists between the EC2 instance and the RDS database, the security groups aren't modified\.  | 
|  Either of the following conditions apply: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/ec2-rds-connect.html)  |  Either of the following conditions apply: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/ec2-rds-connect.html)  |  [RDS action: create new security groups](#rds-action-create-new-security-groups)  | 
|  There are one or more security groups associated with the DB cluster with a name that matches the pattern `rds-ec2-n`\. A security group that matches the pattern hasn't been modified\. This security group has only one inbound rule with the VPC security group of the EC2 instance as the source\.  |  There are one or more security groups associated with the EC2 instance with a name that matches the pattern `ec2-rds-n`\. However, none of these security groups can be used for the connection with the DB cluster\. A security group can't be used if it doesn't have one outbound rule with the VPC security group of the DB cluster as the source\. A security group also can't be used if it has been modified\.  |  [RDS action: create new security groups](#rds-action-create-new-security-groups)  | 
|  There are one or more security groups associated with the DB cluster with a name that matches the pattern `rds-ec2-n`\. A security group that matches the pattern hasn't been modified\. This security group has only one inbound rule with the VPC security group of the EC2 instance as the source\.  |  A valid EC2 security group for the connection exists, but it is not associated with the EC2 instance\. This security group has a name that matches the pattern `rds-ec2-n`\. It hasn't been modified\. It has only one outbound rule with the VPC security group of the DB cluster as the source\.  |  [RDS action: associate EC2 security group](#rds-action-associate-ec2-security-group)  | 
|  Either of the following conditions apply: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/ec2-rds-connect.html)  |  There are one or more security groups associated with the EC2 instance with a name that matches the pattern `rds-ec2-n`\. A security group that matches the pattern hasn't been modified\. This security group has only one outbound rule with the VPC security group of the DB cluster as the source\.  |  [RDS action: create new security groups](#rds-action-create-new-security-groups)  | 

**RDS action: create new security groups**  
RDS takes the following actions:
+ Creates a new security group that matches the pattern `rds-ec2-n`\. This security group has an inbound rule with the VPC security group of the EC2 instance as the source\. This security group is associated with the DB cluster and allows the EC2 instance to access the  DB cluster\.
+ Creates a new security group that matches the pattern `ec2-rds-n`\. This security group has an outbound rule with the VPC security group of the DB cluster as the source\. This security group is associated with the EC2 instance and allows the EC2 instance to send traffic to the DB cluster\.

**RDS action: associate EC2 security group**  
RDS associates the valid, existing EC2 security group with the EC2 instance\. This security group allows the EC2 instance to send traffic to the DB cluster\.

## Connecting an EC2 instance and an Aurora DB cluster automatically<a name="ec2-rds-connect-connecting"></a>

Before setting up a connection between an EC2 instance and an Aurora DB cluster, make sure you meet the requirements described in [Overview of automatic connectivity with an EC2 instance](#ec2-rds-connect-overview)\.

If you change these security groups after you configure connectivity, the changes might affect the connection between the EC2 instance and the Aurora DB cluster\.

**Note**  
You can only set up a connection between an EC2 instance and an Aurora DB cluster automatically by using the AWS Management Console\. You can't set up a connection automatically with the AWS CLI or RDS API\.

**To connect an EC2 instance and an Aurora DB cluster automatically**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the DB cluster\.

1. For **Actions**, choose **Set up EC2 connection**\.

   The **Set up EC2 connection** page appears\.

1. On the **Set up EC2 connection** page, choose the EC2 instance\.  
![\[Set up EC2 connection page.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/auto-connect-rds-ec2-set-up.png)

   If no EC2 instances exist in the same VPC, choose **Create EC2 instance** to create one\. In this case, make sure the new EC2 instance is in the same VPC as the DB cluster\.

1. Choose **Continue**\.

   The **Review and confirm** page appears\.  
![\[EC2 connection review and confirmation page.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/auto-connect-rds-ec2-confirm.png)

1. On the **Review and confirm** page, review the changes that RDS will make to set up connectivity with the EC2 instance\.

   If the changes are correct, choose **Confirm and set up**\.

   If the changes aren't correct, choose **Previous** or **Cancel**\.

## Viewing connected compute resources<a name="ec2-rds-connect-viewing"></a>

You can use the AWS Management Console to view the compute resources that are connected to an Aurora DB cluster\. The resources shown include compute resource connections that were set up automatically\. You can set up connectivity with compute resources automatically in the following ways:
+ You can select the compute resource when you create the database\.

  For more information, see [Creating an Amazon Aurora DB cluster](Aurora.CreateInstance.md)\.
+ You can set up connectivity between an existing database and a compute resource\.

  For more information, see [Connecting an EC2 instance and an Aurora DB cluster automatically](#ec2-rds-connect-connecting)\.

The listed compute resources don't include ones that were connected to the database manually\. For example, you can allow a compute resource to access a database manually by adding a rule to the VPC security group associated with the database\.

For a compute resource to be listed, the following conditions must apply:
+ The name of the security group associated with the compute resource matches the pattern `ec2-rds-n` \(where `n` is a number\)\.
+ The security group associated with the compute resource has an outbound rule with the port range set to the port used by the DB cluster\.
+ The security group associated with the compute resource has an outbound rule with the source set to a security group associated with the DB cluster\.
+ The name of the security group associated with the DB cluster matches the pattern `rds-ec2-n` \(where `n` is a number\)\.
+ The security group associated with the DB cluster has an inbound rule with the port range set to the port used by the DB cluster\.
+ The security group associated with the DB cluster has an inbound rule with the source set to a security group associated with the compute resource\.

**To connect an EC2 instance and an Aurora DB cluster automatically**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the name of the DB cluster\.

1. On the **Connectivity & security** tab, view the compute resources in the **Connected compute resources**\.  
![\[Connected compute resources.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/ec2-connected-compute-resources.png)