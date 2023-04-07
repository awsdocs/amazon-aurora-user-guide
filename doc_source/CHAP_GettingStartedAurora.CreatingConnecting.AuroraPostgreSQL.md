# Creating and connecting to an Aurora PostgreSQL DB cluster<a name="CHAP_GettingStartedAurora.CreatingConnecting.AuroraPostgreSQL"></a>

This tutorial creates an EC2 instance and an Aurora PostgreSQL DB cluster\. The tutorial shows you how to access the DB cluster from the EC2 instance using a standard PostgreSQL client\. As a best practice, this tutorial creates a private DB cluster in a virtual private cloud \(VPC\)\. In most cases, other resources in the same VPC, such as EC2 instances, can access the DB cluster, but resources outside of the VPC can't access it\.

**Important**  
There's no charge for creating an AWS account\. However, by completing this tutorial, you might incur costs for the AWS resources that you use\. You can delete these resources after you complete the tutorial if they are no longer needed\.

The following diagram shows the configuration when the tutorial is complete\.

![\[EC2 instance and Aurora PostgreSQL DB cluster.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/getting-started-aurora-postgresql.png)

This tutorial uses **Easy create** to create a DB cluster running Aurora PostgreSQL with the AWS Management Console\. With **Easy create**, you specify only the DB engine type, DB instance size, and DB cluster identifier\. **Easy create** uses the default settings for the other configuration options\. The DB cluster created by **Easy create** is private\. 

When you use **Standard create** instead of **Easy create**, you can specify more configuration options when you create a DB cluster, including ones for availability, security, backups, and maintenance\. To create a public DB cluster, you must use **Standard create**\. For information about creating DB clusters with **Standard create**, see [Creating an Amazon Aurora DB cluster](Aurora.CreateInstance.md)\.

**Topics**
+ [Prerequisites](#CHAP_GettingStarted.Prerequisites.AuroraPostgreSQL)
+ [Step 1: Create an Aurora PostgreSQL DB cluster](#CHAP_GettingStarted.AuroraPostgreSQL.CreateDBCluster)
+ [Step 2: Create an EC2 instance](#CHAP_GettingStarted.Creating.AuroraPostgreSQL.EC2)
+ [Step 3: Connect your EC2 instance and Aurora PostgreSQL DB cluster automatically](#CHAP_GettingStarted.Connecting.EC2.AuroraPostgreSQL)
+ [Step 4: Connect to an Aurora PostgreSQL DB cluster](#CHAP_GettingStartedAurora.AuroraPostgreSQL.Connect)
+ [Step 5: Delete the EC2 instance and DB cluster](#CHAP_GettingStartedAurora.Deleting.AuroraPostgreSQL)

## Prerequisites<a name="CHAP_GettingStarted.Prerequisites.AuroraPostgreSQL"></a>

Before you begin, complete the steps in the following sections:
+ [Sign up for an AWS account](CHAP_SettingUp_Aurora.md#sign-up-for-aws)
+ [Create an administrative user](CHAP_SettingUp_Aurora.md#create-an-admin)

## Step 1: Create an Aurora PostgreSQL DB cluster<a name="CHAP_GettingStarted.AuroraPostgreSQL.CreateDBCluster"></a>

In this example, you use **Easy create** to create an Aurora PostgreSQL DB cluster with a db\.t4g\.large DB instance class\.

**To create an Aurora PostgreSQL DB cluster with Easy create**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the Amazon RDS console, choose the AWS Region in which you want to create the DB cluster\.

1. In the navigation pane, choose **Databases**\.

1. Choose **Create database**, and make sure that **Easy create** is chosen\.  
![\[Create database section: Easy create option selected.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/easy-create-option.png)

1. In **Configuration**, choose **Aurora \(PostgreSQL Compatible\)** for **Engine type**\.

1. For **DB instance size**, choose **Dev/Test**\.

1. For **DB cluster identifier**, enter **database\-test1**\.

   The **Create database** page should look similar to the following image\.  
![\[Configuration section with selections.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/easy-create-aurora-postgresql.png)

1. For **Master username**, enter a name for the user, or keep the default name \(**postgres**\)\.

1. To use an automatically generated master password for the DB cluster, select **Auto generate a password**\.

   To enter your master password, make sure **Auto generate a password** is cleared, and then enter the same password in **Master password** and **Confirm password**\.

1. Open **View default settings for Easy create**\.  
![\[Easy create default settings.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/easy-create-view-default-settings-aurora-postgresql.png)

   Note the setting for **VPC**\. Your DB cluster and EC2 instance must reside in the same VPC to set up connectivity between them automatically in a later step\. If you didn't create a new VPC in the AWS Region, then the default VPC is selected\.

   You can examine the default settings used with **Easy create**\. The **Editable after database is created** column shows which options you can change after you create the database\.
   + If a setting has **No** in that column, and you want a different setting, you can use **Standard create** to create the DB cluster\.
   + If a setting has **Yes** in that column, and you want a different setting, you can either use **Standard create** to create the DB cluster, or modify the DB cluster after you create it to change the setting\.

1. Choose **Create database**\.

   To view the master username and password for the DB cluster, choose **View credential details**\.

   You can use the username and password that appears to connect to the DB cluster as the master user\.
**Important**  
You can't view the master user password again\. If you don't record it, you might have to change it\.  
If you need to change the master user password after the DB cluster is available, you can modify the DB cluster to do so\. For more information about modifying a DB cluster, see [Modifying an Amazon Aurora DB cluster](Aurora.Modifying.md)\.

1. In the **Databases** list, choose the name of the new Aurora PostgreSQL DB cluster to show its details\.

   The writer instance has a status of **Creating** until the DB cluster is ready to use\.

   Wait for the **Region & AZ** value to appear for the writer instance\. When it appears, make a note of the value because you need it later\. In the following image, the **Region & AZ** value is **us\-west\-1b** for the writer instance\.  
![\[DB cluster details showing Region & AZ and Status.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/creating-status-aurora-postgresql.png)

   When the status of the writer instance changes to **Available**, you can connect to the DB cluster\. Depending on the DB instance class and the amount of storage, it can take up to 20 minutes before the new DB cluster is available\. While the DB cluster is being created, you can move on to the next step and create an EC2 instance\.

## Step 2: Create an EC2 instance<a name="CHAP_GettingStarted.Creating.AuroraPostgreSQL.EC2"></a>

Create an Amazon EC2 instance that you will use to connect to your database\.

**To create an EC2 instance**

1. Sign in to the AWS Management Console and open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the upper\-right corner of the AWS Management Console, choose the AWS Region you used for the database previously\.

1. Choose **EC2 Dashboard**, and then choose **Launch instance**, as shown in the following image\.  
![\[EC2 Dashboard.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/Tutorial_WebServer_11.png)

   The **Launch an instance** page opens\.

1. Choose the following settings on the **Launch an instance** page\.

   1. Under **Name and tags**, for **Name**, enter **ec2\-database\-connect**\.

   1. Under **Application and OS Images \(Amazon Machine Image\)**, choose **Amazon Linux**, and then choose the **Amazon Linux 2 AMI**\. Keep the default selections for the other choices\.  
![\[Choose an Amazon Machine Image.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/Tutorial_WebServer_12.png)

   1. Under **Instance type**, choose **t2\.micro**\.

   1. Under **Key pair \(login\)**, choose a **Key pair name** to use an existing key pair\. To create a new key pair for the Amazon EC2 instance, choose **Create new key pair** and then use the **Create key pair** window to create it\.

      For more information about creating a new key pair, see [Create a key pair](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/get-set-up-for-amazon-ec2.html#create-a-key-pair) in the *Amazon EC2 User Guide for Linux Instances*\.

   1. In **Network settings**, choose **Edit**\.

      1. For **VPC**, choose the VPC that you used for the database\. If you didn't create a new VPC in the AWS Region, choose the default VPC\.

      1. For **Subnet**, choose the subnet that is in the same Availability Zone as the database\. You noted the Availability Zone of the database when you created it previously\. If you don't know the Availability Zone of the database, you can find it in the database details\.

      1. For **Auto\-assign public IP**, make sure **Enable** is selected\.

         If this setting has changed to **Disable**, then there is more than one subnet in the Availability Zone, and **Subnet** is set to a private subnet\. In this case, change the **Subnet** setting to a public subnet in the Availability Zone\.

      1. For **Firewall \(security groups\)**, keep the default values\.

      1. For **Inbound security groups rules**, choose the source of SSH connections to the EC2 instance\.

         For **Type**, choose **ssh**\.

         For **Source type**, choose **My IP** if the displayed IP address is correct for SSH connections\.

         Otherwise, choose **Custom** and specify the IP address or IP address range\. To determine your public IP address, open a different browser window or tab, and use the service at [https://checkip\.amazonaws\.com](https://checkip.amazonaws.com)\. An example of an IP address is `192.0.2.1/32`\.

         In many cases, you might connect through an internet service provider \(ISP\) or from behind your firewall without a static IP address\. If so, make sure to determine the range of IP addresses used by client computers\.
**Warning**  
If you use `0.0.0.0/0` for SSH access, you make it possible for all IP addresses to access your public EC2 instances using SSH\. This approach is acceptable for a short time in a test environment, but it's unsafe for production environments\. In production, authorize only a specific IP address or range of addresses to access your EC2 instances using SSH\.

         The following image shows an example of the **Inbound security groups rules** section\.  
![\[Inbound security group rules for an EC2 instance.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/EC2_RDS_Connect_NtwkSettings.png)

   1. Leave the default values for the remaining sections\.

   1. Review a summary of your EC2 instance configuration in the **Summary** panel, and when you're ready, choose **Launch instance**\.

1. On the **Launch Status** page, note the identifier for your new EC2 instance, for example: `i-1234567890abcdef0`\.  
![\[EC2 instance identifier on Launch Status page.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/getting-started-ec2-id.png)

1. Choose the EC2 instance identifier to open the list of EC2 instances, and then select your EC2 instance\.

1. In the **Details** tab, note the following values, which you need when you connect using SSH:

   1. In **Instance summary**, note the value for **Public IPv4 DNS**\.  
![\[EC2 public DNS name on Details tab of Instances page.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/easy-create-ec2-public-dns.png)

   1. In **Instance details**, note the value for **Key pair name**\.  
![\[EC2 key pair name on Details tab of Instance page.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/easy-create-ec2-key-pair.png)

1. Wait until the **Instance state** for your EC2 instance has a status of **Running** before continuing\.

## Step 3: Connect your EC2 instance and Aurora PostgreSQL DB cluster automatically<a name="CHAP_GettingStarted.Connecting.EC2.AuroraPostgreSQL"></a>

You can automatically connect an existing EC2 instance to a DB cluster using the RDS console\. The RDS console simplifies setting up the connection between an EC2 instance and your Aurora PostgreSQL DB cluster\. For this tutorial, set up a connection between the EC2 instance and the Aurora PostgreSQL DB cluster that you created previously\.

Before setting up a connection between an EC2 instance and an Aurora DB cluster, make sure you meet the requirements described in [Overview of automatic connectivity with an EC2 instance](ec2-rds-connect.md#ec2-rds-connect-overview)\.

If you make changes to security groups after you configure connectivity, the changes might affect the connection between the EC2 instance and the Aurora DB cluster\.

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

To set up connectivity, RDS adds a security group to the EC2 instance and a security group to the DB cluster\.

## Step 4: Connect to an Aurora PostgreSQL DB cluster<a name="CHAP_GettingStartedAurora.AuroraPostgreSQL.Connect"></a>

You can use any standard PostgreSQL client application to connect to the DB cluster\. In this example, you connect to the Aurora PostgreSQL DB cluster using the psql command line client\.

**To connect to the Aurora PostgreSQL DB cluster**

1. Find the endpoint \(DNS name\) and port number of the writer instance for your DB cluster\. 

   1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

   1. In the upper\-right corner of the Amazon RDS console, choose the AWS Region for the DB cluster\.

   1. In the navigation pane, choose **Databases**\.

   1. Choose the Aurora PostgreSQL DB cluster name to display its details\. 

   1. On the **Connectivity & security** tab, copy the endpoint of the writer instance\. Also, note the port number\. You need both the endpoint and the port number to connect to the DB cluster\.   
![\[Connectivity & security tab showing endpoint name.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraPostgreSQLLaunch01.png)

1. Connect to the EC2 instance that you created earlier by following the steps in [Connect to your Linux instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html) in the *Amazon EC2 User Guide for Linux Instances*\.

   We recommend that you connect to your EC2 instance using SSH\. If the SSH client utility is installed on Windows, Linux, or Mac, you can connect to the instance using the following command format:

   ```
   ssh -i location_of_pem_file ec2-user@ec2-instance-public-dns-name
   ```

   For example, assume that `ec2-database-connect-key-pair.pem` is stored in `/dir1` on Linux, and the public IPv4 DNS for your EC2 instance is `ec2-12-345-678-90.compute-1.amazonaws.com`\. Your SSH command would look as follows:

   ```
   ssh -i /dir1/ec2-database-connect-key-pair.pem ec2-user@ec2-12-345-678-90.compute-1.amazonaws.com
   ```

1. Get the latest bug fixes and security updates by updating the software on your EC2 instance\. To do so, use the following command\.
**Note**  
The `-y` option installs the updates without asking for confirmation\. To examine updates before installing, omit this option\.

   ```
   sudo yum update -y
   ```

1. Install the psql command line client from PostgreSQL on Amazon Linux 2, using the following command:

   ```
   sudo amazon-linux-extras install postgresql14
   ```

1. Connect to the Aurora PostgreSQL DB cluster\. For example, enter the following command\. This action lets you connect to the Aurora PostgreSQL DB cluster using the psql client\.

   Substitute the endpoint of the writer instance for `endpoint`, and substitute the master username that you used for `postgres`\. Provide the master password that you used when prompted for a password\.

   ```
   psql --host=endpoint --port=5432 --username=postgres
   ```

   After you enter the password for the user, you should see output similar to the following\.

   ```
   psql (14.3, server 14.6)
   SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
   Type "help" for help.
   
   postgres=>
   ```

   For more information about connecting to an Aurora PostgreSQL DB cluster, see [Connecting to an Amazon Aurora PostgreSQL DB cluster](Aurora.Connecting.md#Aurora.Connecting.AuroraPostgreSQL)\. If you can't connect to your DB cluster, see [Can't connect to Amazon RDS DB instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\.

   For security, it is a best practice to use encrypted connections\. Only use an unencrypted PostgreSQL connection when the client and server are in the same VPC and the network is trusted\. For information about using encrypted connections, see [Securing Aurora PostgreSQL data with SSL/TLS](AuroraPostgreSQL.Security.md#AuroraPostgreSQL.Security.SSL)\.

1. Run SQL commands\.

   For example, the following SQL command shows the current date and time:

   ```
   SELECT CURRENT_TIMESTAMP;
   ```

## Step 5: Delete the EC2 instance and DB cluster<a name="CHAP_GettingStartedAurora.Deleting.AuroraPostgreSQL"></a>

After you connect to and explore the sample EC2 instance and DB cluster that you created, delete them so you're no longer charged for them\.

**To delete the EC2 instance**

1. Sign in to the AWS Management Console and open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation pane, choose **Instances**\.

1. Select the EC2 instance, and choose **Instance state, Terminate instance**\.

1. Choose **Terminate** when prompted for confirmation\.

For more information about deleting an EC2 instance, see [Terminate your instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html) in the *Amazon EC2 User Guide for Linux Instances*\.

**To delete a DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose **Databases** and then choose the DB instance associated with the DB cluster\.

1. For **Actions**, choose **Delete**\.

1. Choose **Delete**\. 

   After all of the DB instances associated with a DB cluster are deleted, the DB cluster is deleted automatically\.