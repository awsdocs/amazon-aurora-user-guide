# Tutorial: Create a Web Server and an Amazon Aurora DB Cluster<a name="TUT_WebAppWithRDS"></a>

This tutorial helps you install an Apache web server with PHP and create a MySQL database\. The web server runs on an Amazon EC2 instance using Amazon Linux, and the MySQL database is an Aurora MySQL DB cluster\. Both the Amazon EC2 instance and the DB cluster run in a virtual private cloud \(VPC\) based on the Amazon VPC service\. 

**Note**  
This tutorial works with Amazon Linux and might not work for other versions of Linux such as Ubuntu\.

In the tutorial that follows, you specify the VPC, subnets, and security groups when you create the DB cluster\. You also specify them when you create the EC2 instance to host your web server\. The VPC, subnets, and security groups are required for the DB cluster and the web server to communicate\. After the VPC is set up, this tutorial shows you how to create the DB cluster and install the web server\. You connect your web server to your DB cluster in the VPC using the DB cluster writer endpoint\.

1. Complete the tasks in [Tutorial: Create an Amazon VPC for Use with a DB Instance](CHAP_Tutorials.WebServerDB.CreateVPC.md)\.

   Before you begin this tutorial, make sure that you have a VPC with both public and private subnets, and corresponding security groups\. If you don't have these, complete the following tasks in the tutorial: 

   1. [Create a VPC with Private and Public Subnets](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.VPCAndSubnets)

   1. [Create Additional Subnets](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.AdditionalSubnets)

   1. [ Create a VPC Security Group for a Public Web Server](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.SecurityGroupEC2)

   1. [ Create a VPC Security Group for a Private DB Instance](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.SecurityGroupDB)

   1. [Create a DB Subnet Group](CHAP_Tutorials.WebServerDB.CreateVPC.md#CHAP_Tutorials.WebServerDB.CreateVPC.DBSubnetGroup)

1. [Create an Amazon Aurora DB Cluster](CHAP_Tutorials.WebServerDB.CreateDBCluster.md)

1. [Create an EC2 Instance and Install a Web Server](CHAP_Tutorials.WebServerDB.CreateWebServer.md)

The following diagram shows the configuration when the tutorial is complete\.

![\[Single VPC Scenario\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/con-VPC-sec-grp.png)