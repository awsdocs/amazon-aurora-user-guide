# Creating an Amazon Aurora DB Cluster<a name="Aurora.CreateInstance"></a>

An Amazon Aurora DB cluster consists of a DB instance, compatible with either MySQL or PostgreSQL, and a cluster volume that represents the data for the DB cluster, copied across three Availability Zones as a single, virtual volume\. By default, the DB cluster contains a primary writer DB instance and, optionally, up to 15 Aurora Replicas \(reader DB instances\)\. For more information about Aurora DB clusters, see [Amazon Aurora DB Clusters](Aurora.Overview.md)\.

In the following topic, you can find out how to create an Aurora DB cluster\. To get started, first see [DB Cluster Prerequisites](#Aurora.CreateInstance.Prerequisites)\.

For simple instructions on connecting to your Aurora DB cluster, see [Connecting to an Amazon Aurora DB Cluster](Aurora.Connecting.md)\.

## DB Cluster Prerequisites<a name="Aurora.CreateInstance.Prerequisites"></a>

**Important**  
Before you can create an Aurora DB cluster, you must complete the tasks in [Setting Up Your Environment for Amazon Aurora](CHAP_SettingUp_Aurora.md)\. 

The following are prerequisites to create a DB cluster\.

### VPC<a name="Aurora.CreateInstance.Prerequisites.VPC"></a>

You can only create an Amazon Aurora DB cluster in a virtual private cloud \(VPC\) based on the Amazon VPC service, in an AWS Region that has at least two Availability Zones\. The DB subnet group that you choose for the DB cluster must cover at least two Availability Zones\. This configuration ensures that your DB cluster always has at least one DB instance available for failover, in the unlikely event of an Availability Zone failure\.

If you are using the AWS Management Console to create your Aurora DB cluster, you can have Amazon RDS automatically create a VPC for you\. Or you can use an existing VPC or create a new VPC for your Aurora DB cluster\. Your VPC must have at least one subnet in each of at least two Availability Zones for you to use it with an Amazon Aurora DB cluster\. For more information, see [How to Create a VPC for Use with Amazon Aurora](Aurora.CreateVPC.md)\. For information on VPCs, see [Amazon Virtual Private Cloud VPCs and Amazon Aurora](USER_VPC.md)\.

**Note**  
You can communicate with an EC2 instance that is not in a VPC and an Amazon Aurora DB cluster using ClassicLink\. For more information, see [A DB Instance in a VPC Accessed by an EC2 Instance Not in a VPC](USER_VPC.Scenarios.md#USER_VPC.ClassicLink)\.

If you don't have a default VPC or you haven't created a VPC, you can have Amazon RDS automatically create a VPC for you when you create an Aurora DB cluster using the console\. Otherwise, you must do the following:
+ Create a VPC with at least one subnet in each of at least two of the Availability Zones in the AWS Region where you want to deploy your DB cluster\. For more information, see [How to Create a VPC for Use with Amazon Aurora](Aurora.CreateVPC.md)\.
+ Specify a VPC security group that authorizes connections to your Aurora DB cluster\. For more information, see [Working with a DB Instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md#Overview.RDSVPC.Create)\.
+ Specify an RDS DB subnet group that defines at least two subnets in the VPC that can be used by the Aurora DB cluster\. For more information, see [Working with DB Subnet Groups](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.Subnets)\.

### Additional Prerequisites<a name="Aurora.CreateInstance.Prerequisites.Additional"></a>

If you are connecting to AWS using AWS Identity and Access Management \(IAM\) credentials, your AWS account must have IAM policies that grant the permissions required to perform Amazon RDS operations\. For more information, see [Identity and Access Management in Amazon Aurora](UsingWithRDS.IAM.md)\.

If you are using IAM to access the Amazon RDS console, you must first sign on to the AWS Management Console with your IAM user credentials\. Then go to the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

If you want to tailor the configuration parameters for your DB cluster, you must specify a DB cluster parameter group and DB parameter group with the required parameter settings\. For information about creating or modifying a DB cluster parameter group or DB parameter group, see [Working with DB Parameter Groups and DB Cluster Parameter Groups](USER_WorkingWithParamGroups.md)\.

You must determine the TCP/IP port number to specify for your DB cluster\. The firewalls at some companies block connections to the default ports \(3306 for MySQL, 5432 for PostgreSQL\) for Aurora\. If your company firewall blocks the default port, choose another port for your DB cluster\. All instances in a DB cluster use the same port\.

## Creating a DB Cluster<a name="Aurora.CreateInstance.Creating"></a>

You can create an Aurora DB cluster using the AWS Management Console, the AWS CLI, or the RDS API\.

**Note**  
If you are using the console, a new console interface is available for database creation\. Choose either the **New Console** or the **Original Console** instructions based on the console that you are using\. The **New Console** instructions are open by default\.

### New Console<a name="Aurora.CreateInstance.Creating.Console"></a>

You can create a DB instance running MySQL with the AWS Management Console with **Easy create** enabled or not enabled\. With **Easy create** enabled, you specify only the DB engine type, DB instance size, and DB instance identifier\. **Easy Create** uses the default setting for other configuration options\. With **Easy create** not enabled, you specify more configuration options when you create a database, including ones for availability, security, backups, and maintenance\.

**Note**  
For this example, **Standard Create** is enabled, and **Easy Create** isn't enabled\. For information about creating an Aurora MySQL DB cluster with **Easy create** enabled, see [Getting Started with Amazon Aurora](CHAP_GettingStartedAurora.md)\.

**To create an Aurora DB cluster using the console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the AWS Management Console, choose the AWS Region in which you want to create the DB cluster\. 

   Aurora is not available in all AWS Regions\. For a list of AWS Regions where Aurora is available, see [Region Availability](Concepts.RegionsAndAvailabilityZones.md#Aurora.Overview.Availability)\.

1. In the navigation pane, choose **Databases**\.

1. Choose **Create database**\.

1. In **Choose a database creation method**, choose **Standard Create**\.

1. In **Engine options**, choose **Amazon Aurora**\.   
![\[Engine options\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraLaunch01.png)

1. In **Edition**, choose one of the following:
   + **Amazon Aurora with MySQL compatibility**
   + **Amazon Aurora with PostgreSQL compatibility**

1. If you chose **Amazon Aurora with MySQL compatibility**, choose one of the following in **Database features**:
   + **One writer and multiple readers**

     For more information, see [Amazon Aurora DB Clusters](Aurora.Overview.md)\.
   + **Serverless**

     For more information, see [Using Amazon Aurora Serverless](aurora-serverless.md)\.

1. In **Templates**, choose the template that matches your use case\.

1. To enter your master password, do the following:

   1. In the **Settings** section, open **Credential Settings**\.

   1. Clear the **Auto generate a password** check box\.

   1. \(Optional\) Change the **Master username** value and enter the same password in **Master password** and **Confirm password**\.

   By default, the new DB instance uses an automatically generated password for the master user\.

1. For the remaining sections, specify your DB cluster settings\. For information about each setting, see [Settings for Aurora DB Clusters](#Aurora.CreateInstance.Settings)\. 

1. Choose **Create database**\. 

   If you chose to use an automatically generated password, the **View credential details** button appears on the **Databases** page\.

   To view the master user name and password for the DB cluster, choose **View credential details**\.  
![\[Master user credentials after automatically generated password.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/easy-create-credentials.png)

   To connect to the DB instance as the master user, use the user name and password that appear\.
**Important**  
You can't view the master user password again\. If you don't record it, you might have to change it\. If you need to change the master user password after the DB instance is available, you can modify the DB instance to do so\. For more information about modifying a DB instance, see [Modifying an Amazon Aurora DB Cluster](Aurora.Modifying.md)\.

1. For **Databases**, choose the name of the new Aurora DB cluster\.

   On the RDS console, the details for new DB cluster appear\. The DB cluster and its DB instance have a status of **creating** until the DB cluster is ready to use\. When the state changes to **available** for both, you can connect to the DB cluster\. Depending on the DB instance class and the amount of storage, it can take up to 20 minutes before the new DB cluster is available\.   
![\[Summary during DB cluster creation\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/creating-status-aurora-mysql.png)

   When the state changes to available, you can connect to the primary instance for your DB cluster\. Depending on the DB instance class and store allocated, it can take several minutes for the new instance to be available\.

   To view the newly created cluster, choose **Databases** from the navigation pane in the Amazon RDS console\. Then choose the DB cluster to show the DB cluster details\. For more information, see [Viewing an Amazon Aurora DB Cluster](Aurora.Viewing.md)\.  
![\[Amazon Aurora DB Instances List\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraLaunch04.png)

   On the **Connectivity & security** tab, note the port and the endpoint of the writer DB instance\. Use the endpoint and port of the cluster in your JDBC and ODBC connection strings for any application that performs write or read operations\.

### Original Console<a name="Aurora.CreateInstance.Creating.CurrentConsole"></a>

**To create an Aurora DB cluster using the AWS Management Console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the top\-right corner of the AWS Management Console, choose the AWS Region in which you want to create the Aurora DB cluster\.

1. In the navigation pane, choose **Databases**\.

   If the navigation pane is closed, choose the menu icon at the top left to open it\.

1. Choose **Create database** to open the **Select engine** page\.

1. On the **Select engine** page, choose an edition of Aurora\. Choose either MySQL 5\.6\-compatible, MySQL 5\.7\-compatible, or PostgreSQL\-compatible\.  
![\[Amazon Aurora Select engine\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/CURRENT-AuroraLaunch01.png)

1. Choose **Next**\.

1. On the **Specify DB details** page, specify your DB instance information\. For information about each setting, see [Settings for Aurora DB Clusters](#Aurora.CreateInstance.Settings)\.

   A typical **Specify DB details** page looks like the following\.   
![\[Amazon Aurora Details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/CURRENT-AuroraLaunch02.png)

1. Confirm your master password and choose **Next**\.

1. On the **Configure advanced settings** page, you can customize additional settings for your Aurora DB cluster\. For information about each setting, see [Settings for Aurora DB Clusters](#Aurora.CreateInstance.Settings)\. 

1. Choose **Create database** to create your Aurora DB cluster, and then choose **Close**\. 

   On the Amazon RDS console, the new DB cluster appears in the list of DB clusters\. The DB cluster will have a status of **creating** until the DB cluster is created and ready for use\. When the state changes to available, you can connect to the writer instance for your DB cluster\. Depending on the DB cluster class and store allocated, it can take several minutes for the new cluster to be available\.

   To view the newly created cluster, choose **Databases** from the navigation pane in the Amazon RDS console and choose the DB cluster to show the DB cluster details\. For more information, see [Viewing an Amazon Aurora DB Cluster](Aurora.Viewing.md)\.  
![\[Amazon Aurora DB Instances List\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/CURRENT-AuroraLaunch04.png)

   Note the ports and the endpoints of the cluster\. Use the endpoint and port of the writer DB cluster in your JDBC and ODBC connection strings for any application that performs write or read operations\.

### AWS CLI<a name="Aurora.CreateInstance.Creating.CLI"></a>

**Note**  
Before you can create an Aurora DB cluster using the AWS CLI, you must fulfill the required prerequisites, such as creating a VPC and an RDS DB subnet group\. For more information, see [DB Cluster Prerequisites](#Aurora.CreateInstance.Prerequisites)\.

You can use the AWS CLI to create an Aurora MySQL DB cluster or an Aurora PostgreSQL DB cluster\.<a name="Aurora.CreateInstance.Creating.CLI.DBCluster.MySQL"></a>

**To create an Aurora MySQL DB cluster using the AWS CLI**

When you create an Aurora MySQL DB cluster or DB instance, ensure that you specify the correct value for the `--engine` option value based on the MySQL compatibility of the DB cluster or DB instance\.
+ When you create an Aurora MySQL 5\.7 DB cluster or DB instance, you must specify `aurora-mysql` for the `--engine` option\.
+ When you create an Aurora MySQL 5\.6 DB cluster or DB instance, you must specify `aurora` for the `--engine` option\.

Complete the following steps:

1. Identify the DB subnet group and VPC security group ID for your new DB cluster, and then call the [create\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) AWS CLI command to create the Aurora MySQL DB cluster\.

   For example, the following command creates a new MySQL 5\.7–compatible DB cluster named `sample-cluster`\.

   For Linux, macOS, or Unix:

   ```
   aws rds create-db-cluster --db-cluster-identifier sample-cluster --engine aurora-mysql \
        --engine-version 5.7.12 --master-username user-name --master-user-password password \
        --db-subnet-group-name mysubnetgroup --vpc-security-group-ids sg-c7e5b0d2
   ```

   For Windows:

   ```
   aws rds create-db-cluster --db-cluster-identifier sample-cluster --engine aurora-mysql ^
       --engine-version 5.7.12 --master-username user-name --master-user-password password ^
       --db-subnet-group-name mysubnetgroup --vpc-security-group-ids sg-c7e5b0d2
   ```

   The following command creates a new MySQL 5\.6–compatible DB cluster named `sample-cluster`\.

   For Linux, macOS, or Unix:

   ```
   aws rds create-db-cluster --db-cluster-identifier sample-cluster --engine aurora \
        --engine-version 5.6.10a --master-username user-name --master-user-password password \
        --db-subnet-group-name mysubnetgroup --vpc-security-group-ids sg-c7e5b0d2
   ```

   For Windows:

   ```
   aws rds create-db-cluster --db-cluster-identifier sample-cluster --engine aurora ^
       --engine-version 5.6.10a --master-username user-name --master-user-password password ^
       --db-subnet-group-name mysubnetgroup --vpc-security-group-ids sg-c7e5b0d2
   ```

1. If you use the console to create a DB cluster, then Amazon RDS automatically creates the primary instance \(writer\) for your DB cluster\. If you use the AWS CLI to create a DB cluster, you must explicitly create the primary instance for your DB cluster\. The primary instance is the first instance that is created in a DB cluster\.

   Call the [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) AWS CLI command to create the primary instance for your DB cluster\. Include the name of the DB cluster as the `--db-cluster-identifier` option value\.

   For example, the following command creates a new MySQL 5\.7–compatible DB instance named `sample-instance`\.

   For Linux, macOS, or Unix:

   ```
   aws rds create-db-instance --db-instance-identifier sample-instance \
        --db-cluster-identifier sample-cluster --engine aurora-mysql --db-instance-class db.r4.large
   ```

   For Windows:

   ```
   aws rds create-db-instance --db-instance-identifier sample-instance ^
        --db-cluster-identifier sample-cluster --engine aurora-mysql --db-instance-class db.r4.large
   ```

   The following command creates a new MySQL 5\.6–compatible DB instance named `sample-instance`\.

   For Linux, macOS, or Unix:

   ```
   aws rds create-db-instance --db-instance-identifier sample-instance \
        --db-cluster-identifier sample-cluster --engine aurora --db-instance-class db.r4.large
   ```

   For Windows:

   ```
   aws rds create-db-instance --db-instance-identifier sample-instance ^
        --db-cluster-identifier sample-cluster --engine aurora --db-instance-class db.r4.large
   ```<a name="Aurora.CreateInstance.Creating.CLI.DBCluster.PostgreSQL"></a>

**To create an Aurora PostgreSQL DB cluster using the AWS CLI**

1. Identify the DB subnet group and VPC security group ID for your new DB cluster, and then call the [create\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) AWS CLI command to create the Aurora PostgreSQL DB cluster\.

   For example, the following command creates a new DB cluster named `sample-cluster`\.

   For Linux, macOS, or Unix:

   ```
   aws rds create-db-cluster --db-cluster-identifier sample-cluster --engine aurora-postgresql \
        --master-username user-name --master-user-password password \
        --db-subnet-group-name mysubnetgroup --vpc-security-group-ids sg-c7e5b0d2
   ```

   For Windows:

   ```
   aws rds create-db-cluster --db-cluster-identifier sample-cluster --engine aurora-postgresql ^
       --master-username user-name --master-user-password password ^
       --db-subnet-group-name mysubnetgroup --vpc-security-group-ids sg-c7e5b0d2
   ```

1. If you use the console to create a DB cluster, then Amazon RDS automatically creates the primary instance \(writer\) for your DB cluster\. If you use the AWS CLI to create a DB cluster, you must explicitly create the primary instance for your DB cluster\. The primary instance is the first instance that is created in a DB cluster\.

   Call the [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) AWS CLI command to create the primary instance for your DB cluster\. Include the name of the DB cluster as the `--db-cluster-identifier` option value\.

   For Linux, macOS, or Unix:

   ```
   aws rds create-db-instance --db-instance-identifier sample-instance \
        --db-cluster-identifier sample-cluster --engine aurora-postgresql --db-instance-class db.r4.large
   ```

   For Windows:

   ```
   aws rds create-db-instance --db-instance-identifier sample-instance ^
        --db-cluster-identifier sample-cluster --engine aurora-postgresql --db-instance-class db.r4.large
   ```

### RDS API<a name="Aurora.CreateInstance.Creating.API"></a>

**Note**  
Before you can create an Aurora DB cluster using the AWS CLI, you must fulfill the required prerequisites, such as creating a VPC and an RDS DB subnet group\. For more information, see [DB Cluster Prerequisites](#Aurora.CreateInstance.Prerequisites)\.

Identify the DB subnet group and VPC security group ID for your new DB cluster, and then call the [CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) operation to create the DB cluster\.

When you create an Aurora MySQL DB cluster or DB instance, ensure that you specify the correct value for the `Engine` parameter value based on the MySQL compatibility of the DB cluster or DB instance\.
+ When you create an Aurora MySQL 5\.7 DB cluster or DB instance, you must specify `aurora-mysql` for the `Engine` parameter\.
+ When you create an Aurora MySQL 5\.6 DB cluster or DB instance, you must specify `aurora` for the `Engine` parameter\.

When you create an Aurora PostgreSQL DB cluster or DB instance, specify `aurora-postgresql` for the `Engine` parameter\.

## Settings for Aurora DB Clusters<a name="Aurora.CreateInstance.Settings"></a>

The following table contains details about settings that you choose when you create an Aurora DB cluster\. 


| For This Option | Do This | 
| --- | --- | 
|   **Availability zone**   |  Determine if you want to specify a particular Availability Zone\. For more information about Availability Zones, see [ Regions and Availability Zones ](Concepts.RegionsAndAvailabilityZones.md)\.  | 
|   **Auto minor version upgrade**   |  Choose **Enable auto minor version upgrade** if you want to enable your Aurora DB cluster to receive preferred minor version upgrades to the DB engine automatically when they become available\. The **Auto minor version upgrade** setting applies to botn Aurora PostgreSQL and Aurora MySQL DB clusters\. For Aurora MySQL 1\.x and 2\.x clusters, this setting upgrades the clusters to a maximum version of 1\.22\.2 and 2\.07\.2\. For more information about engine updates for Aurora PostgreSQL, see [Database Engine Updates for Amazon Aurora PostgreSQL ](AuroraPostgreSQL.Updates.md)\. For more information about engine updates for Aurora MySQL, see [Database Engine Updates for Amazon Aurora MySQL](AuroraMySQL.Updates.md)\.  | 
|   **Backtrack**   |  Applies only to Aurora MySQL\. Choose **Enable Backtrack** to enable backtracking or **Disable Backtrack** to disable backtracking\. Using backtracking, you can rewind a DB cluster to a specific time, without creating a new DB cluster\. It is disabled by default\. If you enable backtracking, also specify the amount of time that you want to be able to backtrack your DB cluster \(the target backtrack window\)\. For more information, see [Backtracking an Aurora DB Cluster](AuroraMySQL.Managing.Backtrack.md)\.  | 
|  Copy tags to snapshots  |  Choose this option to copy any DB instance tags to a DB snapshot when you create a snapshot\.  For more information, see [Tagging Amazon RDS Resources](USER_Tagging.md)\.   | 
|  Database authentication  |  The database authentication option you want to use\. Choose **Password authentication** to authenticate database users with database passwords only\. Choose **Password and IAM DB authentication** to authenticate database users with database passwords and user credentials through IAM users and roles\. For more information, see [IAM Database Authentication](UsingWithRDS.IAMDBAuth.md)\.  | 
|   **Database port**   |  Specify the port for applications and utilities to use to access the database\. Aurora MySQL DB clusters default to the default MySQL port, 3306, and Aurora PostgreSQL DB clusters default to the default PostgreSQL port, 5432\. The firewalls at some companies block connections to these default ports\. If your company firewall blocks the default port, choose another port for the new DB cluster\.  | 
|  **DB cluster identifier**  |  Enter a name for your DB cluster that is unique for your account in the AWS Region that you chose\. This identifier is used in the cluster endpoint address for your DB cluster\. For information on the cluster endpoint, see [Amazon Aurora Connection Management](Aurora.Overview.Endpoints.md)\. The DB cluster identifier has the following constraints: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.CreateInstance.html)  | 
|   **DB cluster parameter group**   |  Choose a DB cluster parameter group\. Aurora has a default DB cluster parameter group you can use, or you can create your own DB cluster parameter group\. For more information about DB cluster parameter groups, see [Working with DB Parameter Groups and DB Cluster Parameter Groups](USER_WorkingWithParamGroups.md)\.  | 
|   **DB engine version**   |  Applies only to the provisioned capacity type\. Choose the version number of your DB engine\.  | 
|   **DB instance class**   |  Applies only to the provisioned capacity type\. Choose a DB instance class that defines the processing and memory requirements for each instance in the DB cluster\. For more information about DB instance classes, see [DB Instance Classes](Concepts.DBInstanceClass.md)\.  | 
|   **DB instance identifier**   |  Enter a name for the primary instance in your DB cluster\. This identifier is used in the endpoint address for the primary instance of your DB cluster\.  The DB instance identifier has the following constraints: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.CreateInstance.html)  | 
|   **DB parameter group**   |  Choose a parameter group\. Aurora has a default parameter group you can use, or you can create your own parameter group\. For more information about parameter groups, see [Working with DB Parameter Groups and DB Cluster Parameter Groups](USER_WorkingWithParamGroups.md)\.  | 
| Enable deletion protection | Choose Enable deletion protection to prevent your DB cluster from being deleted\. If you create a production DB cluster with the console, deletion protection is enabled by default\.  | 
|   **Enable encryption**   |  Choose `Enable encryption` to enable encryption at rest for this DB cluster\. For more information, see [Encrypting Amazon Aurora Resources](Overview.Encryption.md)\.  | 
|  **Enable Enhanced Monitoring**  |  Choose **Enable enhanced monitoring** to enable gathering metrics in real time for the operating system that your DB cluster runs on\. For more information, see [Enhanced Monitoring](USER_Monitoring.OS.md)\.   | 
|   **Enable Performance Insights**   |  Choose `Enable Performance Insights` to enable Amazon RDS Performance Insights\. For more information, see [Using Amazon RDS Performance Insights](USER_PerfInsights.md)\.  | 
|   **Failover priority**   |  Choose a failover priority for the instance\. If you don't choose a value, the default is **tier\-1**\. This priority determines the order in which Aurora Replicas are promoted when recovering from a primary instance failure\. For more information, see [Fault Tolerance for an Aurora DB Cluster](Aurora.Managing.Backups.md#Aurora.Managing.FaultTolerance)\.  | 
|  **Granularity**  |  Only available if **Enhanced Monitoring** is set to **Enable enhanced monitoring**\. Set the interval, in seconds, between when metrics are collected for your DB cluster\.  | 
|   **Initial database name**   |  Enter a name for your default database\. If you don't provide a name, Amazon RDS doesn't create a database on the DB cluster you are creating\. For Aurora MySQL, the default database name has these constraints: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.CreateInstance.html) For Aurora PostgreSQL, the default database name has these constraints: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.CreateInstance.html) To create additional databases, connect to the DB cluster and use the SQL command CREATE DATABASE\. For more information about connecting to the DB cluster, see [Connecting to an Amazon Aurora DB Cluster](Aurora.Connecting.md)\.  | 
| **Log exports** |  Choose the types of MySQL or PostgreSQL database log files to generate\. For more information, see [MySQL Database Log Files](USER_LogAccess.Concepts.MySQL.md) and [PostgreSQL Database Log Files](USER_LogAccess.Concepts.PostgreSQL.md)\.   | 
|   **Maintenance window**   |  Choose **Select window** and specify the weekly time range during which system maintenance can occur\. Or choose **No preference** for Amazon RDS to assign a period randomly\.  | 
|   **Master key**   |  Only available if **Encryption** is set to **Enable encryption**\. Choose the master key to use for encrypting this DB cluster\. For more information, see [Encrypting Amazon Aurora Resources](Overview.Encryption.md)\.  | 
|   **Option group**   |  Aurora has a default option group\.  | 
|   **Master password**   |  Enter a password to log on to your DB cluster: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.CreateInstance.html)  | 
|   **Master username**   |  Enter a name to use as the master user name to log on to your DB cluster: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.CreateInstance.html)  | 
|  **Monitoring Role**  |  Only available if **Enhanced Monitoring** is set to **Enable enhanced monitoring**\. Choose the IAM role that you created to permit Amazon RDS to communicate with Amazon CloudWatch Logs for you, or choose **Default** to have RDS create a role for you named `rds-monitoring-role`\. For more information, see [Enhanced Monitoring](USER_Monitoring.OS.md)\.   | 
|   **Multi\-AZ deployment**   |  Applies only to the provisioned capacity type\. Determine if you want to create Aurora Replicas in other Availability Zones for failover support\. If you choose **Create Replica in Different Zone**, then Amazon RDS creates an Aurora Replica for you in your DB cluster in a different Availability Zone than the primary instance for your DB cluster\. For more information about multiple Availability Zones, see [ Regions and Availability Zones ](Concepts.RegionsAndAvailabilityZones.md)\.   | 
|   **Option group**   |  Aurora has a default option group\.  | 
|  **Performance Insights**  |  Doesn't apply to MySQL 5\.6\. Choose **Enable Performance Insights** if you want to use Amazon RDS Performance Insights to monitor your Amazon Aurora DB cluster load\. For more information about Performance Insights, see [Using Amazon RDS Performance Insights](USER_PerfInsights.md)\.   | 
|   **Publicly accessible**   |  Choose **Yes** to give the DB cluster a public IP address; otherwise, choose **No**\. The instances in your DB cluster can be a mix of both public and private DB instances\. For more information about hiding instances from public access, see [Hiding a DB Instance in a VPC from the Internet](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.Hiding)\. To connect to a DB instance from outside of its Amazon VPC, the DB instance must be publicly accessible, access must be granted using the inbound rules of the DB instance's security group, and other requirements must be met\. For more information, see [Can't Connect to Amazon RDS DB Instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\.  | 
|   **Retention period**   |  Choose the length of time, from 1 to 35 days, that Aurora retains backup copies of the database\. Backup copies can be used for point\-in\-time restores \(PITR\) of your database down to the second\.  | 
|   **Subnet group**   |  Choose the DB subnet group to use for the DB cluster\.  For more information, see [DB Cluster Prerequisites](#Aurora.CreateInstance.Prerequisites)\.  | 
|  **Select the log types to publish to Amazon CloudWatch Logs**  | Applies only to Aurora MySQL\. In the Log exports section, choose the logs that you want to start publishing to Amazon CloudWatch Logs\. For more about publishing to CloudWatch Logs, see [Publishing Amazon Aurora MySQL Logs to Amazon CloudWatch Logs](AuroraMySQL.Integrating.CloudWatch.md)\.  | 
|   **Virtual Private Cloud \(VPC\)**   |  Choose the VPC to host the DB cluster\. Choose **Create a New VPC** to have Amazon RDS create a VPC for you\. For more information, see [DB Cluster Prerequisites](#Aurora.CreateInstance.Prerequisites)\.  | 
|   **VPC security group**   |  Choose **Create new** to have Amazon RDS create a VPC security group for you\. Or choose **Choose existing** and specify one or more VPC security groups to secure network access to the DB cluster\. When you choose **Create new** in the RDS console, a new security group is created with an inbound rule that allows access to the DB instance from the IP address detected in your browser\. For more information, see [DB Cluster Prerequisites](#Aurora.CreateInstance.Prerequisites)\.  | 