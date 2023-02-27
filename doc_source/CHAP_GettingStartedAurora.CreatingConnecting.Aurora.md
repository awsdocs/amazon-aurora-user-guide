# Creating a DB cluster and connecting to a database on an Aurora MySQL DB cluster<a name="CHAP_GettingStartedAurora.CreatingConnecting.Aurora"></a>

The easiest way to create an Aurora MySQL DB cluster is to use the AWS Management Console\. After you create the DB cluster, you can use standard MySQL utilities, such as MySQL Workbench, to connect to a database on the DB cluster\.

**Important**  
Before you can create or connect to a DB cluster, you must complete the tasks in [Setting up your environment for Amazon Aurora](CHAP_SettingUp_Aurora.md)\.

There's no charge for creating an AWS account\. However, by completing this tutorial, you might incur costs for the AWS resources that you use\. You can delete these resources after you complete the tutorial if they are no longer needed\.

**Topics**
+ [Create an Aurora MySQL DB cluster](#CHAP_GettingStarted.Aurora.CreateDBCluster)
+ [Connecting an EC2 instance and an Aurora MySQL DB cluster automatically](#CHAP_GettingStarted.Connecting.EC2.AuroraMySQL)
+ [Connect to an instance in a DB cluster](#CHAP_GettingStartedAurora.Aurora.Connect)
+ [Delete the sample DB cluster, DB subnet group, and VPC](#CHAP_GettingStartedAurora.Deleting.Aurora)

## Create an Aurora MySQL DB cluster<a name="CHAP_GettingStarted.Aurora.CreateDBCluster"></a>

Before you create a DB cluster, you must first have a virtual private cloud \(VPC\) based on the Amazon VPC service and an Amazon RDS DB subnet group\. Your VPC must have at least one subnet in each of at least two Availability Zones\. You can use the default VPC for your AWS account, or you can create your own VPC\. The Amazon RDS console is designed to make it easy for you to create your own VPC for use with Amazon Aurora or use an existing VPC with your Aurora DB cluster\.

In some cases, you might want to create a VPC and DB subnet group for use with your Aurora DB cluster yourself, rather than having Amazon RDS create them\. If so, follow the instructions in [Tutorial: Create a VPC for use with a DB cluster \(IPv4 only\)](CHAP_Tutorials.WebServerDB.CreateVPC.md)\. Otherwise, follow the instructions in this topic to create your DB cluster and have Amazon RDS create a VPC and DB subnet group for you\.

You can use **Easy create** to create an Aurora MySQL\-Compatible Edition DB cluster with the RDS console\. With **Easy create**, you specify only the DB engine type, size, and DB cluster identifier\. **Easy create** uses the default settings for the other configuration options\. When you use **Standard create** instead of **Easy create**, you specify more configuration options when you create a database, including ones for availability, security, backups, and maintenance\.

In this tutorial, you use **Easy create** to create an Aurora MySQL\-Compatible Edition DB cluster\.

**Note**  
For information about creating DB clusters with **Standard create**, see [Creating an Amazon Aurora DB cluster](Aurora.CreateInstance.md)\.

**To create an Aurora MySQL DB cluster with Easy create enabled**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the Amazon RDS console, choose the AWS Region in which you want to create the DB cluster\.

   Aurora is not available in all AWS Regions\. For a list of AWS Regions where Aurora is available, see [Region availability](Concepts.RegionsAndAvailabilityZones.md#Aurora.Overview.Availability)\.

1. In the navigation pane, choose **Databases**\.

1. Choose **Create database**, then choose **Easy create**\.  
![\[Easy create option\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/easy-create-option.png)

1. For **Engine type**, choose **Aurora \(MySQL Compatible\)**\.

1. For **DB instance size**, choose **Dev/Test**\.

1. For **DB cluster identifier**, enter a name for the DB cluster, or leave the default name\.

   The **Create database** page should look similar to the following image\.  
![\[Create database page\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/easy-create-aurora-mysql.png)

1. For **Master username**, enter a name for the user, or leave the default name \(**admin**\)\. This user name and its password give you control over the Aurora MySQL DB cluster\. 

   To use an automatically generated password for the DB cluster, make sure that the **Auto generate a password** box is selected\.

   To choose your own password, clear the **Auto generate a password** box, and then enter the same password in **Master password** and **Confirm password**\.

1. \(Optional\) Open **View default settings for Easy create**\.  
![\[Easy create default settings.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/easy-create-view-default-settings.png)

   You can examine the default settings used with **Easy create**\. The **Editable after database is created** column shows which options you can change after database creation\.
   + To change settings with **No** in that column, use **Standard create**\. 
   + To change settings with **Yes** in that column, either use **Standard create**, or modify the DB cluster after it is created to change the settings\.

   The following are important considerations for changing the default settings:
   + If you want the DB cluster to use a specific VPC, subnet group, and security group, use **Standard create** to specify these resources\. You might have created these resources when you were setting up for Amazon RDS\. For more information, see [Setting up your environment for Amazon Aurora](CHAP_SettingUp_Aurora.md)\.
   + If you want to be able to access the DB cluster from a client outside of its VPC, use **Standard create** to set **Public access** to **Yes**\.

     If the DB cluster should be private, leave **Public access** set to **No**\.

1. Choose **Create database**\.

   If you chose to use an automatically generated password, the **View credential details** button appears on the **Databases** page\.

   To view the user name and password for the DB cluster, choose **View credential details**\.

   To connect to the DB cluster as the master user, use the user name and password that appear\.
**Important**  
You can't view the master user password again\. If you don't record it, you might have to change it\.   
If you need to change the master user password after the DB cluster is available, you can modify the DB cluster to do so\. For more information about modifying a DB cluster, see [Modifying an Amazon Aurora DB cluster](Aurora.Modifying.md)\.

1. For **Databases**, choose the name of the new Aurora MySQL DB cluster\.

   On the RDS console, the details for new DB cluster appear\. The DB cluster and its DB instance have a status of **Creating** until the DB cluster is ready to use\. When the state changes to **Available** for both, you can connect to the DB cluster\. Depending on the DB instance class and the amount of storage, it can take up to 20 minutes before the new DB cluster is available\.   
![\[Summary during DB cluster creation\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/creating-status-aurora-mysql.png)

## Connecting an EC2 instance and an Aurora MySQL DB cluster automatically<a name="CHAP_GettingStarted.Connecting.EC2.AuroraMySQL"></a>

You can automatically connect an existing EC2 instance to the Aurora MySQL DB cluster from the **Create Database** page\. The RDS console simplifies setting up the connection between an EC2 instance and your Aurora MySQL DB cluster\.

Before setting up a connection between an EC2 instance and an Aurora DB cluster, make sure you meet the requirements described in [Overview of automatic connectivity with an EC2 instance](ec2-rds-connect.md#ec2-rds-connect-overview)\.

If you change these security groups after you configure connectivity, the changes might affect the connection between the EC2 instance and the Aurora DB cluster\.

**Note**  
You can only set up a connection between an EC2 instance and an Aurora DB cluster automatically by using the AWS Management Console\. You can't set up a connection automatically with the AWS CLI or RDS API\.

**To connect an EC2 instance and an Aurora DB cluster automatically**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the DB cluster\.

1. For **Actions**, choose **Set up EC2 connection**\.

   The **Set up EC2 connection** page appears\.

1. On the **Set up EC2 connection** page, choose the EC2 instance\.  
![\[Set up EC2 connection page\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/auto-connect-rds-ec2-set-up.png)

   If no EC2 instances exist in the same VPC, choose **Create EC2 instance** to create one\. In this case, make sure the new EC2 instance is in the same VPC as the DB cluster\.

1. Choose **Continue**\.

   The **Review and confirm** page appears\.  
![\[EC2 connection review and confirmation page\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/auto-connect-rds-ec2-confirm.png)

1. On the **Review and confirm** page, review the changes that RDS will make to set up connectivity with the EC2 instance\.

   If the changes are correct, choose **Confirm and set up**\.

   If the changes aren't correct, choose **Previous** or **Cancel**\.

## Connect to an instance in a DB cluster<a name="CHAP_GettingStartedAurora.Aurora.Connect"></a>

After Amazon RDS provisions your DB cluster and creates the primary instance, you can use any standard SQL client application to connect to a database on the DB cluster\. In the following procedure, you connect to a database on the Aurora MySQL DB cluster using MySQL monitor commands\.

**To connect to a database on an Aurora MySQL DB cluster using the MySQL monitor**

1. Install a SQL client that you can use to connect to the DB cluster\.

   You can connect to an Aurora MySQL DB cluster by using tools like the MySQL command line utility\. For more information on using the MySQL client, see [mysql \- the MySQL command\-line client](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) in the MySQL documentation\. One GUI\-based application you can use to connect is MySQL Workbench\. For more information, see the [Download MySQL Workbench](http://dev.mysql.com/downloads/workbench/) page\.

   For more information on using MySQL, see the [MySQL documentation](http://dev.mysql.com/doc/)\. For information about installing MySQL \(including the MySQL client\), see [Installing and upgrading MySQL](https://dev.mysql.com/doc/refman/8.0/en/installing.html)\.

   If your DB cluster is publicly accessible, you can install the SQL client outside of the VPC\. If your DB cluster is private, you typically install the SQL client on a resource inside the VPC, such as an Amazon EC2 instance\.

1. Make sure that your DB cluster is associated with a security group that provides access to it\. For more information, see [Setting up your environment for Amazon Aurora](CHAP_SettingUp_Aurora.md)\.

   If you didn't specify the appropriate security group when you created the DB cluster, you can modify the DB cluster to change its security group\. For more information, see [Modifying an Amazon Aurora DB cluster](Aurora.Modifying.md)\.

   If your DB cluster is publicly accessible, make sure its associated security group has inbound rules for the IP addresses that you want to access it\. If your DB cluster is private, make sure its associated security group has inbound rules for the security group of each resource that you want to access it, such as the security group of an Amazon EC2 instance\.

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose **Databases** and then choose the DB cluster name to show its details\. On the **Connectivity & security** tab, copy the value for the **Endpoint name** of the **Writer instance** endpoint\. Also, note the port number for the endpoint\.  
![\[DB Cluster Details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraLaunch04.png)

1. Enter the following command at a command prompt on a client computer to connect to a database on an Aurora MySQL DB cluster using the MySQL monitor\. Use the cluster endpoint to connect to the primary instance, and the master user name that you created previously\. \(You are prompted for a password\.\) If you supplied a port value other than 3306, use that for the `-P` parameter instead\.

   ```
   PROMPT> mysql -h <cluster endpoint> -P 3306 -u <myusername> -p						
   ```

   You should see output similar to the following\.

   ```
   Welcome to the MySQL monitor.  Commands end with ; or \g.
   Your MySQL connection id is 350
   Server version: 5.6.10-log MySQL Community Server (GPL)
   
   Type 'help;' or '\h' for help. Type '\c' to clear the buffer.
   
   mysql>
   ```

For more information about connecting to the DB cluster, see [Connecting to an Amazon Aurora MySQL DB cluster](Aurora.Connecting.md#Aurora.Connecting.AuroraMySQL)\. If you can't connect to your DB cluster, see [Can't connect to Amazon RDS DB instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\.

## Delete the sample DB cluster, DB subnet group, and VPC<a name="CHAP_GettingStartedAurora.Deleting.Aurora"></a>

After you have connected to the sample DB cluster that you created, you can delete the DB cluster, DB subnet group, and VPC \(if you created a VPC\)\. 

**To delete a DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose **Databases** and then choose the DB instance associated with the DB cluster\.

1. For **Actions**, choose **Delete**\.

1. Choose **Delete**\. 

   After all of the DB instances associated with a DB cluster are deleted, the DB cluster is deleted automatically\.

**To delete a DB subnet group**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose **Subnet groups** and then choose the DB subnet group\.

1. Choose **Delete**\.

1. Choose **Delete**\. 

**To delete a VPC**

1. Sign in to the AWS Management Console and open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. Choose **Your VPCs** and then choose the VPC that was created for this procedure\.

1. For **Actions**, choose **Delete VPC**\.

1. Choose **Delete**\. 