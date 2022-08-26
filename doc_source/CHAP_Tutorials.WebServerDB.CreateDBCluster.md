# Create an Amazon Aurora DB cluster<a name="CHAP_Tutorials.WebServerDB.CreateDBCluster"></a>

In this step, you create an Amazon Aurora MySQL DB cluster that maintains the data used by a web application\. 

**Important**  
Before you begin this step, you must have a VPC with both public and private subnets, and corresponding security groups\. If you don't have these, see [Tutorial: Create a VPC for use with a DB cluster \(IPv4 only\)](CHAP_Tutorials.WebServerDB.CreateVPC.md)\. Complete the steps in [Create a VPC with private and public subnets](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.VPCAndSubnets), [Create a VPC security group for a public web server](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.SecurityGroupEC2), and [Create a VPC security group for a private DB cluster](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.SecurityGroupDB)\. 

**To create an Aurora MySQL DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the AWS Management Console, choose the AWS Region where you want to create the DB cluster\. This example uses the US West \(Oregon\) Region\.

1. In the navigation pane, choose **Databases**\.

1. Choose **Create database**\.

1. On the **Create database** page, shown following, make sure that the **Standard create** option is chosen, and then choose **Amazon Aurora**\. Keep the default values for **Version** and the other engine options\.   
![\[Select engine\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraLaunch01.png)

1. In the **Templates** section, choose **Dev/Test**\.

1. In the **Availability and durability** section, use the default values\.

1. In the **Settings** section, set these values:
   + **DB cluster identifier** – **tutorial\-db\-cluster**
   + **Master username** – **tutorial\_user**
   + **Auto generate a password** – Disable the option\.
   + **Master password** – Choose a password\.
   + **Confirm password** – Retype the password\.  
![\[Settings sections\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/Tutorial_WebServer_Settings-Aurora.png)

1. In the **Instance configuration** section, set these values:
   + **Burstable classes \(includes t classes\)**
   + **db\.t3\.micro**  
![\[Instance configuration section\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/Tutorial_WebServer_DB_instance_micro.png)

1. In the **Connectivity** section, set these values:
   + **Virtual private cloud \(VPC\)** – Choose an existing VPC with both public and private subnets, such as the `tutorial-vpc` \(vpc\-*identifier*\) created in [Create a VPC with private and public subnets](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.VPCAndSubnets)\.
**Note**  
The VPC must have subnets in different Availability Zones\.
   + **DB subnet group** – Choose a DB subnet group for the VPC, such as the `tutorial-db-subnet-group` created in [Create a DB subnet group](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.DBSubnetGroup)\.
   + **Public access** – Choose **No**\.
   + **VPC security group \(firewall\)** – Select **Choose existing**\.
   + **Existing VPC security groups** – Choose an existing VPC security group that is configured for private access, such as the `tutorial-db-securitygroup` created in [Create a VPC security group for a private DB cluster](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.SecurityGroupDB)\.

     Remove other security groups, such as the default security group, by choosing the **X** associated with each\.
   + **Availability Zone** – Choose **us\-west\-2a**\.

     To avoid cross\-AZ traffic, make sure the DB cluster and the EC2 instance are in the same Availability Zone\.
   + Open **Additional configuration**, and make sure **Database port** uses the default value **3306**\.  
![\[Connectivity section\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/Tutorial_WebServer_Connectivity_Cluster.png)

1. Open the **Additional configuration** section, and enter **sample** for **Initial database name**\. Keep the default settings for the other options\.

1. To create your Aurora MySQL DB cluster, choose **Create database**\.

   Your new DB cluster appears in the **Databases** list with the status **Creating**\.

1. Wait for the **Status** of your new DB cluster to show as **Available**\. Then choose the DB cluster name to show its details\.

1. In the **Connectivity & security** section, view the **Endpoint** and **Port** of the writer DB instance\.  
![\[DB cluster details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/Tutorial_WebServer_Endpoint_Port_Aurora.png)

   Note the endpoint and port for your writer DB instance\. You use this information to connect your web server to your DB cluster\.

1. Complete [Create an EC2 instance and install a web server](CHAP_Tutorials.WebServerDB.CreateWebServer.md)\.