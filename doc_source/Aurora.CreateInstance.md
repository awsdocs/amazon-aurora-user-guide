# Creating an Amazon Aurora DB cluster<a name="Aurora.CreateInstance"></a><a name="create_cluster"></a>

An Amazon Aurora DB cluster consists of a DB instance, compatible with either MySQL or PostgreSQL, and a cluster volume that holds the data for the DB cluster, copied across three Availability Zones as a single, virtual volume\. By default, an Aurora DB cluster contains a primary DB instance that performs reads and writes, and, optionally, up to 15 Aurora Replicas \(reader DB instances\)\. For more information about Aurora DB clusters, see [Amazon Aurora DB clusters](Aurora.Overview.md)\.

Aurora has two main types of DB cluster:
+ Aurora provisioned – You choose the DB instance class for the writer and reader instances based on your expected workload\. For more information, see [Aurora DB instance classes](Concepts.DBInstanceClass.md)\. Aurora provisioned has several options, including Aurora global databases\. For more information, see [Using Amazon Aurora global databases](aurora-global-database.md)\.
+ Aurora Serverless – Aurora Serverless v1 and Aurora Serverless v2 are on\-demand automatic scaling configurations for Aurora\. Capacity is adjusted automatically based on application demand\. You're charged only for the resources that your DB cluster consumes\. This automation is especially useful for environments with highly variable and unpredictable workloads\. For more information, see [Using Amazon Aurora Serverless v1](aurora-serverless.md) and [Using Aurora Serverless v2](aurora-serverless-v2.md)\.

Following, you can find out how to create an Aurora DB cluster\. To get started, first see [DB cluster prerequisites](#Aurora.CreateInstance.Prerequisites)\.

For instructions on connecting to your Aurora DB cluster, see [Connecting to an Amazon Aurora DB cluster](Aurora.Connecting.md)\.

**Topics**
+ [DB cluster prerequisites](#Aurora.CreateInstance.Prerequisites)
+ [Creating a DB cluster](#Aurora.CreateInstance.Creating)
+ [Settings for Aurora DB clusters](#Aurora.CreateInstance.Settings)
+ [Settings that don't apply to Amazon Aurora for DB clusters](#Aurora.CreateDBCluster.SettingsNotApplicableDBClusters)
+ [Settings that don't apply to Amazon Aurora DB instances](#Aurora.CreateInstance.SettingsNotApplicable)

## DB cluster prerequisites<a name="Aurora.CreateInstance.Prerequisites"></a>

**Important**  
Before you can create an Aurora DB cluster, you must complete the tasks in [Setting up your environment for Amazon Aurora](CHAP_SettingUp_Aurora.md)\.

The following are prerequisites to complete before creating a DB cluster\.

**Topics**
+ [Configure the network for the DB cluster](#Aurora.CreateInstance.Prerequisites.VPC)
+ [Additional prerequisites](#Aurora.CreateInstance.Prerequisites.Additional)

### Configure the network for the DB cluster<a name="Aurora.CreateInstance.Prerequisites.VPC"></a>

You can create an Amazon Aurora DB cluster only in a virtual private cloud \(VPC\) based on the Amazon VPC service, in an AWS Region that has at least two Availability Zones\. The DB subnet group that you choose for the DB cluster must cover at least two Availability Zones\. This configuration ensures that your DB cluster always has at least one DB instance available for failover, in the unlikely event of an Availability Zone failure\.

If you plan to set up connectivity between your new DB cluster and an EC2 instance in the same VPC, you can do so during DB cluster creation\. If you plan to connect to your DB cluster from resources other than EC2 instances in the same VPC, you can configure the network connections manually\.

**Topics**
+ [Configure automatic network connectivity with an EC2 instance](#Aurora.CreateInstance.Prerequisites.VPC.Automatic)
+ [Configure the network manually](#Aurora.CreateInstance.Prerequisites.VPC.Manual)

#### Configure automatic network connectivity with an EC2 instance<a name="Aurora.CreateInstance.Prerequisites.VPC.Automatic"></a>

When you create an Aurora DB cluster, you can use the AWS Management Console to set up connectivity between an Amazon EC2 instance and the new DB cluster\. When you do so, RDS configures your VPC and network settings automatically\. The DB cluster is created in the same VPC as the EC2 instance so that the EC2 instance can access the DB cluster\.

The following are requirements for connecting an EC2 instance with the DB cluster:
+ The EC2 instance must exist in the AWS Region before you create the DB cluster\.

  If no EC2 instances exist in the AWS Region, the console provides a link to create one\.
+ Currently, the DB cluster can't be an Aurora Serverless DB cluster or part of an Aurora global database\.
+ The user who is creating the DB instance must have permissions to perform the following operations:
  + `ec2:AssociateRouteTable` 
  + `ec2:AuthorizeSecurityGroupEgress` 
  + `ec2:AuthorizeSecurityGroupIngress` 
  + `ec2:CreateRouteTable` 
  + `ec2:CreateSubnet` 
  + `ec2:CreateSecurityGroup` 
  + `ec2:DescribeInstances` 
  + `ec2:DescribeNetworkInterfaces` 
  + `ec2:DescribeRouteTables` 
  + `ec2:DescribeSecurityGroups` 
  + `ec2:DescribeSubnets` 
  + `ec2:ModifyNetworkInterfaceAttribute` 
  + `ec2:RevokeSecurityGroupEgress` 

Using this option creates a private DB cluster\. The DB cluster uses a DB subnet group with only private subnets to restrict access to resources within the VPC\.

To connect an EC2 instance to the DB cluster, choose **Connect to an EC2 compute resource** in the **Connectivity** section on the **Create database** page\.

![\[Connect an EC2 instance\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/ec2-set-up-connection-create.png)

When you choose **Connect to an EC2 compute resource**, RDS sets the following options automatically\. You can't change these settings unless you choose not to set up connectivity with an EC2 instance by choosing **Don't connect to an EC2 compute resource**\.


****  

| Console option | Automatic setting | 
| --- | --- | 
|  **Network type**  |  RDS sets network type to **IPv4**\. Currently, dual\-stack mode isn't supported when you set up a connection between an EC2 instance and the DB cluster\.  | 
|  **Virtual Private Cloud \(VPC\)**  |  RDS sets the VPC to the one associated with the EC2 instance\.  | 
|  **DB subnet group**  | RDS requires a DB subnet group with a private subnet in the same Availability Zone as the EC2 instance\. If a DB subnet group that meets this requirement exists, then RDS uses the existing DB subnet group\. By default, this option is set to Automatic setup\. When you choose **Automatic setup** and there is no DB subnet group that meets this requirement, the following action happens\. RDS uses three available private subnets in three Availability Zones where one of the Availability Zones is the same as the EC2 instance\. If a private subnet isn’t available in an Availability Zone, RDS creates a private subnet in the Availability Zone\. Then RDS creates the DB subnet group\.When a private subnet is available, RDS uses the route table associated with the subnet and adds any subnets it creates to this route table\. When no private subnet is available, RDS creates a route table without internet gateway access and adds the subnets it creates to the route table\.RDS also allows you to use existing DB subnet groups\. Select **Choose existing** if you want to use an existing DB subnet group of your choice\. | 
|  **Public access**  |  RDS chooses **No** so that the DB cluster isn't publicly accessible\. For security, it is a best practice to keep the database private and make sure it isn't accessible from the internet\.  | 
|  **VPC security group \(firewall\)**  |  RDS creates a new security group that is associated with the DB cluster\. The security group is named `rds-ec2-n`, where `n` is a number\. This security group includes an inbound rule with the EC2 VPC security group \(firewall\) as the source\. This security group that is associated with the DB cluster allows the EC2 instance to access the DB cluster\. RDS also creates a new security group that is associated with the EC2 instance\. The security group is named `ec2-rds-n`, where `n` is a number\. This security group includes an outbound rule with the VPC security group of the DB cluster as the source\. This security group allows the EC2 instance to send traffic to the DB cluster\. You can add another new security group by choosing **Create new** and typing the name of the new security group\. You can add existing security groups by choosing **Choose existing** and selecting security groups to add\.  | 
|  **Availability Zone**  |  When you don't create an Aurora Replica in **Availability & durability** during DB cluster creation \(Single\-AZ deployment\), RDS chooses the Availability Zone of the EC2 instance\. When you create an Aurora Replica during DB cluster creation \(Multi\-AZ deployment\), RDS chooses the Availability Zone of the EC2 instance for one DB instance in the DB cluster\. RDS randomly chooses a different Availability Zone for the other DB instance in the DB cluster\. Either the primary DB instance or the Aurora Replica is created in the same Availability Zone as the EC2 instance\. There is the possibility of cross Availability Zone costs if the primary DB instance and EC2 instance are in different Availability Zones\.  | 

For more information about these settings, see [Settings for Aurora DB clusters](#Aurora.CreateInstance.Settings)\.

If you make any changes to these settings after the DB cluster is created, the changes might affect the connection between the EC2 instance and the DB cluster\.

#### Configure the network manually<a name="Aurora.CreateInstance.Prerequisites.VPC.Manual"></a>

If you plan to connect to your DB cluster from resources other than EC2 instances in the same VPC, you can configure the network connections manually\. If you use the AWS Management Console to create your DB cluster, you can have Amazon RDS automatically create a VPC for you\. Or you can use an existing VPC or create a new VPC for your Aurora DB cluster\. Whichever approach you take, your VPC must have at least one subnet in each of at least two Availability Zones for you to use it with an Amazon Aurora DB cluster\. 

By default, Amazon RDS creates the primary DB instance and the Aurora Replica in the Availability Zones automatically for you\. To choose a specific Availability Zone, you need to change the **Availability & durability** Multi\-AZ deployment setting to **Don't create an Aurora Replica**\. Doing so exposes an **Availability Zone** setting that lets you choose from among the Availability Zones in your VPC\. However, we strongly recommend that you keep the default setting and let Amazon RDS create a Multi\-AZ deployment and choose Availability Zones for you\. By doing so, your Aurora DB cluster is created with the fast failover and high availability features that are two of Aurora's key benefits\. 

If you don't have a default VPC or you haven't created a VPC, you can have Amazon RDS automatically create a VPC for you when you create a DB cluster using the console\. Otherwise, you must do the following:
+ Create a VPC with at least one subnet in each of at least two of the Availability Zones in the AWS Region where you want to deploy your DB cluster\. For more information, see [Working with a DB cluster in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md#Overview.RDSVPC.Create) and [Tutorial: Create a VPC for use with a DB cluster \(IPv4 only\)](CHAP_Tutorials.WebServerDB.CreateVPC.md)\.
+ Specify a VPC security group that authorizes connections to your DB cluster\. For more information, see [Provide access to the DB cluster in the VPC by creating a security group](CHAP_SettingUp_Aurora.md#CHAP_SettingUp_Aurora.SecurityGroup) and [Controlling access with security groups](Overview.RDSSecurityGroups.md)\.
+ Specify an RDS DB subnet group that defines at least two subnets in the VPC that can be used by the DB cluster\. For more information, see [Working with DB subnet groups](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.Subnets)\.

 For information on VPCs, see [Amazon VPC VPCs and Amazon Aurora](USER_VPC.md)\. For a tutorial that configures the network for a private DB cluster, see [Tutorial: Create a VPC for use with a DB cluster \(IPv4 only\)](CHAP_Tutorials.WebServerDB.CreateVPC.md)\.

If you want to connect to a resource that isn't in the same VPC as the Aurora DB cluster, see the appropriate scenarios in [Scenarios for accessing a DB cluster in a VPC](USER_VPC.Scenarios.md)\.

### Additional prerequisites<a name="Aurora.CreateInstance.Prerequisites.Additional"></a>

Before you create your DB cluster, consider the following additional prerequisites:
+ If you are connecting to AWS using AWS Identity and Access Management \(IAM\) credentials, your AWS account must have IAM policies that grant the permissions required to perform Amazon RDS operations\. For more information, see [Identity and access management for Amazon Aurora](UsingWithRDS.IAM.md)\.

  If you are using IAM to access the Amazon RDS console, you must first sign on to the AWS Management Console with your user credentials\. Then go to the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.
+ If you want to tailor the configuration parameters for your DB cluster, you must specify a DB cluster parameter group and DB parameter group with the required parameter settings\. For information about creating or modifying a DB cluster parameter group or DB parameter group, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.
+ Determine the TCP/IP port number to specify for your DB cluster\. The firewalls at some companies block connections to the default ports \(3306 for MySQL, 5432 for PostgreSQL\) for Aurora\. If your company firewall blocks the default port, choose another port for your DB cluster\. All instances in a DB cluster use the same port\.

## Creating a DB cluster<a name="Aurora.CreateInstance.Creating"></a>

You can create an Aurora DB cluster using the AWS Management Console, the AWS CLI, or the RDS API\.

### Console<a name="Aurora.CreateInstance.Creating.Console"></a>

You can create a DB instance running MySQL with the AWS Management Console with **Easy create** enabled or not enabled\. With **Easy create** enabled, you specify only the DB engine type, DB instance size, and DB instance identifier\. **Easy create** uses the default setting for other configuration options\. With **Easy create** not enabled, you specify more configuration options when you create a database, including ones for availability, security, backups, and maintenance\.

**Note**  
For this example, **Standard create** is enabled, and **Easy create** isn't enabled\. For information about creating an Aurora MySQL DB cluster with **Easy create** enabled, see [Getting started with Amazon Aurora](CHAP_GettingStartedAurora.md)\.

**To create an Aurora DB cluster using the console**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the AWS Management Console, choose the AWS Region in which you want to create the DB cluster\. 

   Aurora is not available in all AWS Regions\. For a list of AWS Regions where Aurora is available, see [Region availability](Concepts.RegionsAndAvailabilityZones.md#Aurora.Overview.Availability)\.

1. In the navigation pane, choose **Databases**\.

1. Choose **Create database**\.

1. In **Choose a database creation method**, choose **Standard create**\.

1. In **Engine options**, choose **Amazon Aurora**\.   
![\[Engine options\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraLaunch01.png)

1. In **Edition**, choose one of the following:
   + **Amazon Aurora with MySQL compatibility**
   + **Amazon Aurora with PostgreSQL compatibility**

1. Choose one of the following in **Capacity type**:
   + **Provisioned**

     For more information, see [Amazon Aurora DB clusters](Aurora.Overview.md)\.
   + **Serverless**

     For more information, see [Using Aurora Serverless v2](aurora-serverless-v2.md) and [Using Amazon Aurora Serverless v1](aurora-serverless.md)\.

1. For **Version**, choose the engine version\.

1. In **Templates**, choose the template that matches your use case\.

1. To enter your master password, do the following:

   1. In the **Settings** section, open **Credential Settings**\.

   1. Clear the **Auto generate a password** check box\.

   1. \(Optional\) Change the **Master username** value and enter the same password in **Master password** and **Confirm password**\.

   By default, the new DB instance uses an automatically generated password for the master user\.

1. \(Optional\) Set up a connection to a compute resource for this DB cluster\.

   You can configure connectivity between an Amazon EC2 instance and the new DB cluster during DB cluster creation\. For more information, see [Configure automatic network connectivity with an EC2 instance](#Aurora.CreateInstance.Prerequisites.VPC.Automatic)\.

1. For the remaining sections, specify your DB cluster settings\. For information about each setting, see [Settings for Aurora DB clusters](#Aurora.CreateInstance.Settings)\. 

1. Choose **Create database**\. 

   If you chose to use an automatically generated password, the **View credential details** button appears on the **Databases** page\.

   To view the master user name and password for the DB cluster, choose **View credential details**\.

   To connect to the DB instance as the master user, use the user name and password that appear\.
**Important**  
You can't view the master user password again\. If you don't record it, you might have to change it\. If you need to change the master user password after the DB instance is available, you can modify the DB instance to do so\. For more information about modifying a DB instance, see [Modifying an Amazon Aurora DB cluster](Aurora.Modifying.md)\.

1. For **Databases**, choose the name of the new Aurora DB cluster\.

   On the RDS console, the details for new DB cluster appear\. The DB cluster and its DB instance have a status of **creating** until the DB cluster is ready to use\.  
![\[Summary during DB cluster creation\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/creating-status-aurora-mysql.png)

   When the state changes to **available** for both, you can connect to the DB cluster\. Depending on the DB instance class and the amount of storage, it can take up to 20 minutes before the new DB cluster is available\.

   To view the newly created cluster, choose **Databases** from the navigation pane in the Amazon RDS console\. Then choose the DB cluster to show the DB cluster details\. For more information, see [Viewing an Amazon Aurora DB cluster](accessing-monitoring.md#Aurora.Viewing)\.  
![\[Amazon Aurora DB Instances List\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraLaunch04.png)

   On the **Connectivity & security** tab, note the port and the endpoint of the writer DB instance\. Use the endpoint and port of the cluster in your JDBC and ODBC connection strings for any application that performs write or read operations\.

### AWS CLI<a name="Aurora.CreateInstance.Creating.CLI"></a>

**Note**  
Before you can create an Aurora DB cluster using the AWS CLI, you must fulfill the required prerequisites, such as creating a VPC and an RDS DB subnet group\. For more information, see [DB cluster prerequisites](#Aurora.CreateInstance.Prerequisites)\.

You can use the AWS CLI to create an Aurora MySQL DB cluster or an Aurora PostgreSQL DB cluster\.<a name="Aurora.CreateInstance.Creating.CLI.DBCluster.MySQL"></a>

**To create an Aurora MySQL DB cluster using the AWS CLI**

When you create an Aurora MySQL DB cluster or DB instance, ensure that you specify the correct value for the `--engine` option value based on the MySQL compatibility of the DB cluster or DB instance\.
+ When you create an Aurora MySQL 8\.0\-compatible or 5\.7\-compatible DB cluster or DB instance, you specify `aurora-mysql` for the `--engine` option\.
+ When you create an Aurora MySQL 5\.6\-compatible DB cluster or DB instance, you specify `aurora` for the `--engine` option\.

Complete the following steps:

1. Identify the DB subnet group and VPC security group ID for your new DB cluster, and then call the [create\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) AWS CLI command to create the Aurora MySQL DB cluster\.

   For example, the following command creates a new MySQL 8\.0–compatible DB cluster named `sample-cluster`\.

   For Linux, macOS, or Unix:

   ```
   aws rds create-db-cluster --db-cluster-identifier sample-cluster --engine aurora-mysql \
        --engine-version 8.0 --master-username user-name --master-user-password password \
        --db-subnet-group-name mysubnetgroup --vpc-security-group-ids sg-c7e5b0d2
   ```

   For Windows:

   ```
   aws rds create-db-cluster --db-cluster-identifier sample-cluster --engine aurora-mysql ^
       --engine-version 8.0 --master-username user-name --master-user-password password ^
       --db-subnet-group-name mysubnetgroup --vpc-security-group-ids sg-c7e5b0d2
   ```

   The following command creates a new MySQL 5\.7–compatible DB cluster named `sample-cluster`\.

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

   For example, the following command creates a new MySQL 5\.7–compatible or MySQL 8\.0–compatible DB instance named `sample-instance`\.

   For Linux, macOS, or Unix:

   ```
   aws rds create-db-instance --db-instance-identifier sample-instance \
        --db-cluster-identifier sample-cluster --engine aurora-mysql --db-instance-class db.r5.large
   ```

   For Windows:

   ```
   aws rds create-db-instance --db-instance-identifier sample-instance ^
        --db-cluster-identifier sample-cluster --engine aurora-mysql --db-instance-class db.r5.large
   ```

   The following command creates a new MySQL 5\.6–compatible DB instance named `sample-instance`\.

   For Linux, macOS, or Unix:

   ```
   aws rds create-db-instance --db-instance-identifier sample-instance \
        --db-cluster-identifier sample-cluster --engine aurora --db-instance-class db.r5.large
   ```

   For Windows:

   ```
   aws rds create-db-instance --db-instance-identifier sample-instance ^
        --db-cluster-identifier sample-cluster --engine aurora --db-instance-class db.r5.large
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
Before you can create an Aurora DB cluster using the AWS CLI, you must fulfill the required prerequisites, such as creating a VPC and an RDS DB subnet group\. For more information, see [DB cluster prerequisites](#Aurora.CreateInstance.Prerequisites)\.

Identify the DB subnet group and VPC security group ID for your new DB cluster, and then call the [CreateDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) operation to create the DB cluster\.

When you create an Aurora MySQL DB cluster or DB instance, ensure that you specify the correct value for the `Engine` parameter value based on the MySQL compatibility of the DB cluster or DB instance\.
+ When you create an Aurora MySQL 5\.7 DB cluster or DB instance, you must specify `aurora-mysql` for the `Engine` parameter\.
+ When you create an Aurora MySQL 5\.6 DB cluster or DB instance, you must specify `aurora` for the `Engine` parameter\.

When you create an Aurora PostgreSQL DB cluster or DB instance, specify `aurora-postgresql` for the `Engine` parameter\.

## Settings for Aurora DB clusters<a name="Aurora.CreateInstance.Settings"></a>

The following table contains details about settings that you choose when you create an Aurora DB cluster\. 

**Note**  
Additional settings are available if you are creating an Aurora Serverless v1 DB cluster\. For information about these settings, see [Creating an Aurora Serverless v1 DB cluster](aurora-serverless.create.md)\. Also, some settings aren't available for Aurora Serverless v1 because of Aurora Serverless v1 limitations\. For more information, see [Limitations of Aurora Serverless v1](aurora-serverless.md#aurora-serverless.limitations)\.


| Console setting | Setting description | CLI option and RDS API parameter | 
| --- | --- | --- | 
|   **Auto minor version upgrade**   |  Choose **Enable auto minor version upgrade** if you want to enable your Aurora DB cluster to receive preferred minor version upgrades to the DB engine automatically when they become available\. The **Auto minor version upgrade** setting applies to both Aurora PostgreSQL and Aurora MySQL DB clusters\. For Aurora MySQL version 1 and version 2 clusters, this setting upgrades the clusters to a maximum version of 1\.22\.2 and 2\.07\.2, respectively\. For more information about engine updates for Aurora PostgreSQL, see [Amazon Aurora PostgreSQL updates](AuroraPostgreSQL.Updates.md)\. For more information about engine updates for Aurora MySQL, see [Database engine updates for Amazon Aurora MySQL](AuroraMySQL.Updates.md)\.  |   Set this value for every DB instance in your Aurora cluster\. If any DB instance in your cluster has this setting turned off, the cluster isn't automatically upgraded\.  Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) and set the `--auto-minor-version-upgrade\|--no-auto-minor-version-upgrade` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) and set the `AutoMinorVersionUpgrade` parameter\.  | 
|   **AWS KMS key**   |  Only available if **Encryption** is set to **Enable encryption**\. Choose the AWS KMS key to use for encrypting this DB cluster\. For more information, see [Encrypting Amazon Aurora resources](Overview.Encryption.md)\.  |  Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) and set the `--kms-key-id` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) and set the `KmsKeyId` parameter\.  | 
|   **Backtrack**   |  Applies only to Aurora MySQL\. Choose **Enable Backtrack** to enable backtracking or **Disable Backtrack** to disable backtracking\. Using backtracking, you can rewind a DB cluster to a specific time, without creating a new DB cluster\. It is disabled by default\. If you enable backtracking, also specify the amount of time that you want to be able to backtrack your DB cluster \(the target backtrack window\)\. For more information, see [Backtracking an Aurora DB cluster](AuroraMySQL.Managing.Backtrack.md)\.  |  Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) and set the `--backtrack-window` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) and set the `BacktrackWindow` parameter\.  | 
|  **Certificate authority**  |  The certificate authority \(CA\) for the server certificate used by the DB instances in the DB cluster\. For more information, see [Using SSL/TLS to encrypt a connection to a DB cluster](UsingWithRDS.SSL.md)\.   |  Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) and set the `--ca-certificate-identifier` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) and set the `CACertificateIdentifier` parameter\.  | 
|  Copy tags to snapshots  |  Choose this option to copy any DB instance tags to a DB snapshot when you create a snapshot\.  For more information, see [Tagging Amazon RDS resources](USER_Tagging.md)\.   |  Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) and set the `--copy-tags-to-snapshot \| --no-copy-tags-to-snapshot` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) and set the `CopyTagsToSnapshot` parameter\.  | 
|  Database authentication  |  The database authentication you want to use\. For MySQL: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.CreateInstance.html) For PostgreSQL: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.CreateInstance.html)  |  To use IAM database authentication with the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) and set the `--enable-iam-database-authentication \| --no-enable-iam-database-authentication` option\. To use IAM database authentication with the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) and set the `EnableIAMDatabaseAuthentication` parameter\. To use Kerberos authentication with the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) and set the `--domain` and `--domain-iam-role-name` options\. To use Kerberos authentication with the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) and set the `Domain` and `DomainIAMRoleName` parameters\.  | 
|   **Database port**   |  Specify the port for applications and utilities to use to access the database\. Aurora MySQL DB clusters default to the default MySQL port, 3306, and Aurora PostgreSQL DB clusters default to the default PostgreSQL port, 5432\. The firewalls at some companies block connections to these default ports\. If your company firewall blocks the default port, choose another port for the new DB cluster\.  |  Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) and set the `--port` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) and set the `Port` parameter\.  | 
|  **DB cluster identifier**  |  Enter a name for your DB cluster that is unique for your account in the AWS Region that you chose\. This identifier is used in the cluster endpoint address for your DB cluster\. For information on the cluster endpoint, see [Amazon Aurora connection management](Aurora.Overview.Endpoints.md)\. The DB cluster identifier has the following constraints: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.CreateInstance.html)  |  Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) and set the `--db-cluster-identifier` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) and set the `DBClusterIdentifier` parameter\.  | 
|   **DB cluster parameter group**   |  Choose a DB cluster parameter group\. Aurora has a default DB cluster parameter group you can use, or you can create your own DB cluster parameter group\. For more information about DB cluster parameter groups, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.  |  Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) and set the `--db-cluster-parameter-group-name` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) and set the `DBClusterParameterGroupName` parameter\.  | 
|   **DB instance class**   |  Applies only to the provisioned capacity type\. Choose a DB instance class that defines the processing and memory requirements for each instance in the DB cluster\. For more information about DB instance classes, see [Aurora DB instance classes](Concepts.DBInstanceClass.md)\.  |   Set this value for every DB instance in your Aurora cluster\.  Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) and set the `--db-instance-class` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) and set the `DBInstanceClass` parameter\.  | 
|   **DB parameter group**   |  Choose a parameter group\. Aurora has a default parameter group you can use, or you can create your own parameter group\. For more information about parameter groups, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\.  |   Set this value for every DB instance in your Aurora cluster\.  Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) and set the `--db-parameter-group-name` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) and set the `DBParameterGroupName` parameter\.  | 
|   **DB subnet group**   | The DB subnet group you want to use for the DB cluster\. Select Choose existing to use an existing DB subnet group\. Then choose the required subnet group from the Existing DB subnet groups dropdown list\.Choose **Automatic setup** to let RDS select a compatible DB subnet group\. If none exist, RDS creates a new subnet group for your cluster\.For more information, see [DB cluster prerequisites](#Aurora.CreateInstance.Prerequisites)\. |  Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) and set the `--db-subnet-group-name` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) and set the `DBSubnetGroupName` parameter\.  | 
| Enable deletion protection | Choose Enable deletion protection to prevent your DB cluster from being deleted\. If you create a production DB cluster with the console, deletion protection is enabled by default\.  |  Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) and set the `--deletion-protection \| --no-deletion-protection` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) and set the `DeletionProtection` parameter\.  | 
|   **Enable encryption**   |  Choose `Enable encryption` to enable encryption at rest for this DB cluster\. For more information, see [Encrypting Amazon Aurora resources](Overview.Encryption.md)\.  |  Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) and set the `--storage-encrypted \| --no-storage-encrypted` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) and set the `StorageEncrypted` parameter\.  | 
|  **Enable Enhanced Monitoring**  |  Choose **Enable enhanced monitoring** to enable gathering metrics in real time for the operating system that your DB cluster runs on\. For more information, see [Monitoring OS metrics with Enhanced Monitoring](USER_Monitoring.OS.md)\.   |   Set these values for every DB instance in your Aurora cluster\.  Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) and set the `--monitoring-interval` and `--monitoring-role-arn` options\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) and set the `MonitoringInterval` and `MonitoringRoleArn` parameters\.  | 
|  Engine type  |  Choose the database engine to be used for this DB cluster\.  |  Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) and set the `--engine` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) and set the `Engine` parameter\.  | 
|   **Engine version**   |  Applies only to the provisioned capacity type\. Choose the version number of your DB engine\.  |  Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) and set the `--engine-version` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) and set the `EngineVersion` parameter\.  | 
|   **Failover priority**   |  Choose a failover priority for the instance\. If you don't choose a value, the default is **tier\-1**\. This priority determines the order in which Aurora Replicas are promoted when recovering from a primary instance failure\. For more information, see [Fault tolerance for an Aurora DB cluster](Concepts.AuroraHighAvailability.md#Aurora.Managing.FaultTolerance)\.  |   Set this value for every DB instance in your Aurora cluster\.  Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) and set the `--promotion-tier` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) and set the `PromotionTier` parameter\.  | 
|   **Initial database name**   |  Enter a name for your default database\. If you don't provide a name for an Aurora MySQL DB cluster, Amazon RDS doesn't create a database on the DB cluster you are creating\. If you don't provide a name for an Aurora PostgreSQL DB cluster, Amazon RDS creates a database named `postgres`\. For Aurora MySQL, the default database name has these constraints: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.CreateInstance.html) For Aurora PostgreSQL, the default database name has these constraints: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.CreateInstance.html) To create additional databases, connect to the DB cluster and use the SQL command CREATE DATABASE\. For more information about connecting to the DB cluster, see [Connecting to an Amazon Aurora DB cluster](Aurora.Connecting.md)\.  |  Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) and set the `--database-name` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) and set the `DatabaseName` parameter\.  | 
| **Log exports** | In the Log exports section, choose the logs that you want to start publishing to Amazon CloudWatch Logs\. For more information about publishing Aurora MySQL logs to CloudWatch Logs, see [Publishing Amazon Aurora MySQL logs to Amazon CloudWatch Logs](AuroraMySQL.Integrating.CloudWatch.md)\. For more information about publishing Aurora PostgreSQL logs to CloudWatch Logs, see [Publishing Aurora PostgreSQL logs to Amazon CloudWatch Logs](AuroraPostgreSQL.CloudWatch.md)\.  |  Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) and set the `--enable-cloudwatch-logs-exports` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) and set the `EnableCloudwatchLogsExports` parameter\.  | 
|   **Maintenance window**   |  Choose **Select window** and specify the weekly time range during which system maintenance can occur\. Or choose **No preference** for Amazon RDS to assign a period randomly\.  |  Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) and set the `--preferred-maintenance-window` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) and set the `PreferredMaintenanceWindow` parameter\.  | 
|   **Manage master credentials in AWS Secrets Manager**   |  Select **Manage master credentials in AWS Secrets Manager** to manage the master user password in a secret in Secrets Manager\. Optionally, choose a KMS key to use to protect the secret\. Choose from the KMS keys in your account, or enter the key from a different account\. For more information, see [Password management with Amazon Aurora and AWS Secrets Manager](rds-secrets-manager.md)\.  |  Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) and set the `--manage-master-user-password \| --no-manage-master-user-password` and `--master-user-secret-kms-key-id` options\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) and set the `ManageMasterUserPassword` and `MasterUserSecretKmsKeyId` parameters\.  | 
|   **Master password**   |  Enter a password to log on to your DB cluster: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.CreateInstance.html)  |  Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) and set the `--master-user-password` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) and set the `MasterUserPassword` parameter\.  | 
|   **Master username**   |  Enter a name to use as the master user name to log on to your DB cluster: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.CreateInstance.html) You can't change the master user name after the DB cluster is created\.  |  Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) and set the `--master-username` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) and set the `MasterUsername` parameter\.  | 
|   **Multi\-AZ deployment**   |  Applies only to the provisioned capacity type\. Determine if you want to create Aurora Replicas in other Availability Zones for failover support\. If you choose **Create Replica in Different Zone**, then Amazon RDS creates an Aurora Replica for you in your DB cluster in a different Availability Zone than the primary instance for your DB cluster\. For more information about multiple Availability Zones, see [Regions and Availability Zones](Concepts.RegionsAndAvailabilityZones.md)\.   |  Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) and set the `--availability-zones` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) and set the `AvailabilityZones` parameter\.  | 
|   **Network type**   |  The IP addressing protocols supported by the DB cluster\. **IPv4** to specify that resources can communicate with the DB cluster only over the IPv4 addressing protocol\. **Dual\-stack mode** to specify that resources can communicate with the DB cluster over IPv4, IPv6, or both\. Use dual\-stack mode if you have any resources that must communicate with your DB cluster over the IPv6 addressing protocol\. To use dual\-stack mode, make sure at least two subnets spanning two Availability Zones that support both the IPv4 and IPv6 network protocol\. Also, make sure you associate an IPv6 CIDR block with subnets in the DB subnet group you specify\. For more information, see [Amazon Aurora IP addressing](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.IP_addressing)\.  |  Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) and set the `-network-type` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) and set the `NetworkType` parameter\.  | 
|   **Public access**   |  Choose **Publicly accessible** to give the DB cluster a public IP address, or choose **Not publicly accessible**\. The instances in your DB cluster can be a mix of both public and private DB instances\. For more information about hiding instances from public access, see [Hiding a DB cluster in a VPC from the internet](USER_VPC.WorkingWithRDSInstanceinaVPC.md#USER_VPC.Hiding)\. To connect to a DB instance from outside of its Amazon VPC, the DB instance must be publicly accessible, access must be granted using the inbound rules of the DB instance's security group, and other requirements must be met\. For more information, see [Can't connect to Amazon RDS DB instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\. If your DB instance is isn't publicly accessible, you can also use an AWS Site\-to\-Site VPN connection or an AWS Direct Connect connection to access it from a private network\. For more information, see [Internetwork traffic privacy](inter-network-traffic-privacy.md)\.  |   Set this value for every DB instance in your Aurora cluster\.  Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) and set the `--publicly-accessible \| --no-publicly-accessible` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) and set the `PubliclyAccessible` parameter\.  | 
|   **RDS Proxy**   |  Choose **Create an RDS Proxy** to create a proxy for your DB cluster\. Amazon RDS automatically creates an IAM role and a Secrets Manager secret for the proxy\. For more information, see [Using Amazon RDS Proxy for Aurora](rds-proxy.md)\.   |  Not available when creating a DB cluster\.  | 
|   **Retention period**   |  Choose the length of time, from 1 to 35 days, that Aurora retains backup copies of the database\. Backup copies can be used for point\-in\-time restores \(PITR\) of your database down to the second\.  |  Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) and set the `--backup-retention-period` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) and set the `BackupRetentionPeriod` parameter\.  | 
|  **Turn on DevOps Guru**   |  Choose **Turn on DevOps Guru** to turn on Amazon DevOps Guru for your Aurora database\. For DevOps Guru for RDS to provide detailed analysis of performance anomalies, Performance Insights must be turned on\. For more information, see [Setting up DevOps Guru for RDS](devops-guru-for-rds.md#devops-guru-for-rds.configuring)\.  |  You can turn on DevOps Guru for RDS from within the RDS console, but not by using the RDS API or CLI\. For more information about turning on DevOps Guru, see the [https://docs.aws.amazon.com/devops-guru/latest/userguide/getting-started.html](https://docs.aws.amazon.com/devops-guru/latest/userguide/getting-started.html)\.  | 
|  **Turn on Performance Insights**   |  Choose **Turn on Performance Insights** to turn on Amazon RDS Performance Insights\. For more information, see [Monitoring DB load with Performance Insights on Amazon Aurora](USER_PerfInsights.md)\.  |   Set these values for every DB instance in your Aurora cluster\.  Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) and set the `--enable-performance-insights \| --no-enable-performance-insights`, `--performance-insights-kms-key-id`, and `--performance-insights-retention-period` options\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) and set the `EnablePerformanceInsights`, `PerformanceInsightsKMSKeyId`, and `PerformanceInsightsRetentionPeriod` parameters\.  | 
|   **Virtual Private Cloud \(VPC\)**   |  Choose the VPC to host the DB cluster\. Choose **Create a New VPC** to have Amazon RDS create a VPC for you\. For more information, see [DB cluster prerequisites](#Aurora.CreateInstance.Prerequisites)\.  |  For the AWS CLI and API, you specify the VPC security group IDs\.  | 
|   **VPC security group \(firewall\)**   |  Choose **Create new** to have Amazon RDS create a VPC security group for you\. Or choose **Choose existing** and specify one or more VPC security groups to secure network access to the DB cluster\. When you choose **Create new** in the RDS console, a new security group is created with an inbound rule that allows access to the DB instance from the IP address detected in your browser\. For more information, see [DB cluster prerequisites](#Aurora.CreateInstance.Prerequisites)\.  |  Using the AWS CLI, run [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) and set the `--vpc-security-group-ids` option\. Using the RDS API, call [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) and set the `VpcSecurityGroupIds` parameter\.  | 

## Settings that don't apply to Amazon Aurora for DB clusters<a name="Aurora.CreateDBCluster.SettingsNotApplicableDBClusters"></a>

The following settings in the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) and the RDS API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) don't apply to Amazon Aurora DB clusters\.

**Note**  
The AWS Management Console doesn't show these settings for Aurora DB clusters\.


****  

| AWS CLI setting | RDS API setting | 
| --- | --- | 
|  `--allocated-storage`  |  `AllocatedStorage`  | 
|  `--auto-minor-version-upgrade \| --no-auto-minor-version-upgrade`  |  `AutoMinorVersionUpgrade`  | 
|  `--db-cluster-instance-class`  |  `DBClusterInstanceClass`  | 
|  `--enable-performance-insights \| --no-enable-performance-insights`  |  `EnablePerformanceInsights`  | 
|  `--iops`  |  `Iops`  | 
|  `--monitoring-interval`  |  `MonitoringInterval`  | 
|  `--monitoring-role-arn`  |  `MonitoringRoleArn`  | 
|  `--option-group-name`  |  `OptionGroupName`  | 
|  `--performance-insights-kms-key-id`  |  `PerformanceInsightsKMSKeyId`  | 
|  `--performance-insights-retention-period`  |  `PerformanceInsightsRetentionPeriod`  | 
|  `--publicly-accessible \| --no-publicly-accessible`  |  `PubliclyAccessible`  | 
|  `--storage-type`  |  `StorageType`  | 

## Settings that don't apply to Amazon Aurora DB instances<a name="Aurora.CreateInstance.SettingsNotApplicable"></a>

The following settings in the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) and the RDS API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) don't apply to DB instances Amazon Aurora DB cluster\.

**Note**  
The AWS Management Console doesn't show these settings for Aurora DB instances\.


****  

| AWS CLI setting | RDS API setting | 
| --- | --- | 
|  `--allocated-storage`  |  `AllocatedStorage`  | 
|  `--availability-zone`  |  `AvailabilityZone`  | 
|  `--backup-retention-period`  |  `BackupRetentionPeriod`  | 
|  `--backup-target`  |  `BackupTarget`  | 
|  `--character-set-name`  |  `CharacterSetName`  | 
|  `--character-set-name`  |  `CharacterSetName`  | 
|  `--custom-iam-instance-profile`  |  `CustomIamInstanceProfile`  | 
|  `--db-security-groups`  |  `DBSecurityGroups`  | 
|  `--deletion-protection \| --no-deletion-protection`  |  `DeletionProtection`  | 
|  `--domain`  |  `Domain`  | 
|  `--domain-iam-role-name`  |  `DomainIAMRoleName`  | 
|  `--enable-cloudwatch-logs-exports`  |  `EnableCloudwatchLogsExports`  | 
|  `--enable-customer-owned-ip \| --no-enable-customer-owned-ip`  |  `EnableCustomerOwnedIp`  | 
|  `--enable-iam-database-authentication \| --no-enable-iam-database-authentication`  |  `EnableIAMDatabaseAuthentication`  | 
|  `--engine-version`  |  `EngineVersion`  | 
|  `--iops`  |  `Iops`  | 
|  `--kms-key-id`  |  `KmsKeyId`  | 
|  `--license-model`  |  `LicenseModel`  | 
|  `--master-username`  |  `MasterUsername`  | 
|  `--master-user-password`  |  `MasterUserPassword`  | 
|  `--max-allocated-storage`  |  `MaxAllocatedStorage`  | 
|  `--multi-az \| --no-multi-az`  |  `MultiAZ`  | 
|  `--nchar-character-set-name`  |  `NcharCharacterSetName`  | 
|  `--network-type`  |  `NetworkType`  | 
|  `--option-group-name`  |  `OptionGroupName`  | 
|  `--preferred-backup-window`  |  `PreferredBackupWindow`  | 
|  `--processor-features`  |  `ProcessorFeatures`  | 
|  `--storage-encrypted \| --no-storage-encrypted`  |  `StorageEncrypted`  | 
|  `--storage-type`  |  `StorageType`  | 
|  `--tde-credential-arn`  |  `TdeCredentialArn`  | 
|  `--tde-credential-password`  |  `TdeCredentialPassword`  | 
|  `--timezone`  |  `Timezone`  | 
|  `--vpc-security-group-ids`  |  `VpcSecurityGroupIds`  | 