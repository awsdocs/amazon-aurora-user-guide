# Creating a DB Cluster and Connecting to a Database on an Aurora PostgreSQL DB Cluster<a name="CHAP_GettingStartedAurora.CreatingConnecting.AuroraPostgreSQL"></a>

The easiest way to create an Aurora PostgreSQL DB cluster is to use the Amazon RDS console\. After you create the DB cluster, you can use standard PostgreSQL utilities, such as pgAdmin, to connect to a database on the DB cluster\.

**Topics**
+ [Create an Aurora PostgreSQL DB Cluster](#CHAP_GettingStarted.AuroraPostgreSQL.CreateDBCluster)
+ [Connect to an Instance in an Aurora PostgreSQL DB Cluster](#CHAP_GettingStartedAurora.AuroraPostgreSQL.Connect)
+ [Delete the Sample DB Cluster, DB Subnet Group, and VPC](#CHAP_GettingStartedAurora.Deleting.AuroraPostgreSQL)

## Create an Aurora PostgreSQL DB Cluster<a name="CHAP_GettingStarted.AuroraPostgreSQL.CreateDBCluster"></a>

Before you create a DB cluster, you must first have a virtual private cloud \(VPC\) based on the Amazon VPC service and an Amazon RDS DB subnet group\. Your VPC must have at least one subnet in each of at least two Availability Zones\. You can use the default VPC for your AWS account, or you can create your own VPC\. The Amazon RDS console makes it easy for you to create your own VPC for use with Amazon Aurora or use an existing VPC with your Aurora DB cluster\.

If you want to create a VPC and DB subnet group for use with your Amazon Aurora DB cluster yourself, rather than having Amazon RDS create the VPC and DB subnet group for you, follow the instructions in [How to Create a VPC for Use with Amazon Aurora](Aurora.CreateVPC.md)\. Otherwise, follow the instructions in this topic to create your DB cluster and have Amazon RDS create a VPC and DB subnet group for you\.

**Note**  
A new console interface is available for database creation\. Choose either the **New Console** or the **Original Console** instructions based on the console that you are using\. The **New Console** instructions are open by default\.

### New Console<a name="CHAP_GettingStarted.AuroraPostgreSQL.CreateDBCluster.Console"></a>

You can create an Aurora PostgreSQL DB cluster with the AWS Management Console with **Easy Create** enabled or disabled\. With **Easy Create** enabled, you specify only the DB engine type, DB instance size, and DB instance identifier\. **Easy Create** uses the default setting for other configuration options\. With **Easy Create** disabled, you specify more configuration options when you create a database, including ones for availability, security, backups, and maintenance\.

**Note**  
For this example, **Easy Create** is enabled\. For information about creating an Aurora PostgreSQL DB cluster with **Easy Create** not enabled, see [Creating an Amazon Aurora DB Cluster](Aurora.CreateInstance.md)\.

**To create an Aurora PostgreSQL DB cluster with Easy Create enabled**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the Amazon RDS console, choose the AWS Region in which you want to create the DB instance\.

   Aurora is not available in all AWS Regions\. For a list of AWS Regions where Aurora is available, see [Region Availability](Concepts.RegionsAndAvailabilityZones.md#Aurora.Overview.Availability)\.

1. In the navigation pane, choose **Databases**\.

1. Choose **Create database** and make sure that **Easy Create** is chosen\.   
![\[Easy Create option\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/easy-create-option.png)

1. For **Engine type**, choose **Amazon Aurora**\.

1. For **Edition**, choose **Amazon Aurora with PostgreSQL compatibility**\.

1. For **DB instance size**, choose **Dev/Test**\.

1. For **DB cluster identifier**, enter a name for the DB cluster, or leave the default name\.

1. For **Master username**, enter a name for the master user, or leave the default name\.

   The **Create database** page should look similar to the following image\.  
![\[Create database page\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/easy-create-aurora-postgresql.png)

1. To use an automatically generated master password for the DB cluster, make sure that the **Auto generate a password** check box is chosen\.

   To enter your master password, clear the **Auto generate a password** check box, and then enter the same password in **Master password** and **Confirm password**\.

1. \(Optional\) Open **View default settings for Easy create**\.  
![\[Easy Create default settings.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/easy-create-view-default-settings.png)

   You can examine the default settings used when **Easy Create** is enabled\. If you want to change one or more settings during database creation, choose **Standard Create** to set them\. The **Editable after database creation** column shows which options can be changed after database creation\. To change a setting with **No** in that column, use **Standard Create**\. For settings with **Yes** in that column, you can either use **Standard Create**, or you can modify the DB instance after it is created to change the setting\.

1. Choose **Create database**\.

   If you chose to use an automatically generated password, the **View credential details** button appears on the **Databases** page\.

   To view the master user name and password for the DB instance, choose **View credential details**\.  
![\[Master user credentials automatic password generation.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/easy-create-credentials.png)

   To connect to the DB instance as the master user, use the user name and password that appear\.
**Important**  
You can't view the master user password again\. If you don't record it, you might have to change it\. If you need to change the master user password after the DB instance is available, you can modify the DB instance to do so\. For more information about modifying a DB instance, see [Modifying an Amazon Aurora DB Cluster](Aurora.Modifying.md)\.

1. For **Databases**, choose the name of the new Aurora PostgreSQL DB cluster\.

   On the RDS console, the details for new DB cluster appear\. The DB cluster and its DB instance have a status of **creating** until the DB cluster is ready to use\. When the state changes to **available** for both, you can connect to the DB cluster\. Depending on the DB instance class and the amount of storage, it can take up to 20 minutes before the new DB cluster is available\.   
![\[Summary during DB cluster creation\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/creating-status-aurora-postgresql.png)

### Original Console<a name="CHAP_GettingStarted.AuroraPostgreSQL.CreateDBCluster.CurrentConsole"></a>

**To launch an Aurora PostgreSQL DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the top\-right corner of the AWS Management Console, choose the AWS Region that you want to create your DB cluster in\. For a list of AWS Regions where Aurora is available, see [Region Availability](Concepts.RegionsAndAvailabilityZones.md#Aurora.Overview.Availability)\.

1. In the navigation pane, choose **Databases**\.

   If the navigation pane is closed, choose the menu icon at the top left to open it\.

1. Choose **Create database** to open the **Select engine** page\.

1. On the **Select engine** page, choose Amazon Aurora and choose the PostgreSQL\-compatible edition\.  
![\[Amazon Aurora Select engine page\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/CURRENT-AuroraPostgresLaunch01.png)

1. Choose **Next**\.

1. Set the following values on the **Specify DB details** page: 
   + **DB instance class:** `db.r4.large`
   + **DB instance identifier:** `aurora-postgres-db-instance1`
   + **Master username:** Using alphanumeric characters, type a master user name, used to log on to your DB instances in the DB cluster\.
   + **Master password** and **Confirm Password:** Type a password in the **Master Password** box that contains from 8 to 41 printable ASCII characters \(excluding /,", and @\) for your master user password, used to log on to your database\. Then type the password again in the **Confirm Password** box\.  
![\[Specify DB details page\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/CURRENT-AuroraPostgresLaunch02.png)

1. Choose **Next** and set the following values on the **Configure Advanced Settings** page: 
   + **Virtual Private Cloud \(VPC\):** If you have an existing VPC, then you can use that VPC with your Amazon Aurora DB cluster by choosing your VPC identifier, for example `vpc-a464d1c1`\. For information on using an existing VPC, see [How to Create a VPC for Use with Amazon Aurora](Aurora.CreateVPC.md)\.

     Otherwise, you can choose to have Amazon RDS create a VPC for you by choosing **Create a new VPC**\. This example uses the **Create a new VPC** option\.
   + **Subnet group:** If you have an existing subnet group, then you can use that subnet group with your Amazon Aurora DB cluster by choosing your subnet group identifier, for example, `gs-subnet-group1`\.

     Otherwise, you can choose to have Amazon RDS create a subnet group for you by choosing **Create a new subnet group**\. This example uses the **Create a new subnet group** option\.
   + **Public accessibility:** `Yes`
**Note**  
Your production DB cluster might not need to be in a public subnet, because only your application servers require access to your DB cluster\. If your DB cluster doesn't need to be in a public subnet, set **Publicly Accessible** to `No`\.
   + **Availability zone:** `No Preference`
   + **VPC security groups:** If you have one or more existing VPC security groups, then you can use one or more of those VPC security groups with your Amazon Aurora DB cluster by choosing your VPC security group identifiers, for example, `gs-security-group1`\.

     Otherwise, you can choose to have Amazon RDS create a VPC security group for you by choosing **Create new VPC security group**\. This example uses the **Create new VPC security group** option\.
   + **DB cluster identifier:** `aurora-postgres-db-cluster1`
   + **Database name:** `sampledb`
**Note**  
This creates the default database\. To create additional databases, connect to the DB cluster and use the SQL command `CREATE DATABASE`\. 
   + **Database port:** `5432`
**Note**  
You might be behind a corporate firewall that does not allow access to default ports such as the Aurora PostgreSQL default port, 5432\. In this case, provide a port value that your corporate firewall allows\. Remember that port value later when you connect to the Aurora DB cluster\.

1. Leave the rest of the values as their defaults, and choose **Create database** to create the DB cluster and primary instance\.

## Connect to an Instance in an Aurora PostgreSQL DB Cluster<a name="CHAP_GettingStartedAurora.AuroraPostgreSQL.Connect"></a>

Once Amazon RDS provisions your DB cluster and creates the primary instance, you can use any standard SQL client application to connect to a database on the DB cluster\.

**To connect to a database on an Aurora PostgreSQL DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose **Databases** and then choose the DB cluster name to show its details\. On the **Connectivity & security** tab, copy the value for the **Endpoint name** of the **Writer** endpoint\. Also, note the port number for the endpoint\.  
![\[DB Cluster Details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-postgresql-endpoint.png)

1. For information about connecting to the DB cluster using the endpoint and port, see [Connecting to an Amazon Aurora PostgreSQL DB Cluster](Aurora.Connecting.md#Aurora.Connecting.AuroraPostgreSQL)

## Delete the Sample DB Cluster, DB Subnet Group, and VPC<a name="CHAP_GettingStartedAurora.Deleting.AuroraPostgreSQL"></a>

Once you have connected to the sample DB cluster that you created, you can delete the DB cluster, DB subnet group, and VPC \(if you created a VPC\)\. 

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