# Getting started with Amazon Aurora global databases<a name="aurora-global-database-getting-started"></a>

To get started with Aurora global databases, first decide which Aurora DB engine you want to use and in which AWS Regions\. Only specific versions of the Aurora MySQL and Aurora PostgreSQL database engines in certain AWS Regions support Aurora global databases\. For the complete list, see [Aurora global databases](Concepts.AuroraFeaturesRegionsDBEngines.grids.md#Concepts.Aurora_Fea_Regions_DB-eng.Feature.GlobalDatabase)\. 

You can create an Aurora global database in one of the following ways:
+ **Create a new Aurora global database with new Aurora DB clusters and Aurora DB instances** – You can do this by following the steps in [Creating an Amazon Aurora global database](#aurora-global-database-creating)\. After you create the primary Aurora DB cluster, you then add the secondary AWS Region by following the steps in [Adding an AWS Region to an Amazon Aurora global database](#aurora-global-database-attaching)\. 
+ **Use an existing Aurora DB cluster that supports the Aurora global database feature and add an AWS Region to it** – You can do this only if your existing Aurora DB cluster uses a DB engine version that supports the Aurora global mode or is global\-compatible\. For some DB engine versions, this mode is explicit, but for others, it's not\. 

  Check whether you can choose **Add region** for **Action** on the AWS Management Console when your Aurora DB cluster is selected\. If you can, you can use that Aurora DB cluster for your Aurora global cluster\. For more information, see [Adding an AWS Region to an Amazon Aurora global database](#aurora-global-database-attaching)\. 

Before creating an Aurora global database, we recommend that you understand all configuration requirements\.

**Topics**
+ [Configuration requirements of an Amazon Aurora global database](#aurora-global-database.configuration.requirements)
+ [Creating an Amazon Aurora global database](#aurora-global-database-creating)
+ [Adding an AWS Region to an Amazon Aurora global database](#aurora-global-database-attaching)
+ [Creating a headless Aurora DB cluster in a secondary Region](#aurora-global-database-attach.console.headless)
+ [Using a snapshot for your Amazon Aurora global database](#aurora-global-database.use-snapshot)

## Configuration requirements of an Amazon Aurora global database<a name="aurora-global-database.configuration.requirements"></a>

An Aurora global database spans at least two AWS Regions\. The primary AWS Region supports an Aurora DB cluster that has one writer Aurora DB instance\. A secondary AWS Region runs a read\-only Aurora DB cluster made up entirely of Aurora Replicas\. At least one secondary AWS Region is required, but an Aurora global database can have up to five secondary AWS Regions\. The table lists the maximum Aurora DB clusters, Aurora DB instances, and Aurora Replicas allowed in an Aurora global database\. 


| Description | Primary AWS Region | Secondary AWS Regions | 
| --- | --- | --- | 
| Aurora DB clusters | 1 | 5 \(maximum\)  | 
| Writer instances | 1 | 0 | 
| Read\-only instances \(Aurora replicas\), per Aurora DB cluster | 15 \(max\) | 16 \(total\) | 
| Read\-only instances \(max allowed, given actual number of secondary regions\) | 15 \- *s* | *s* = total number of secondary AWS Regions  | 

The Aurora DB clusters that make up an Aurora global database have the following specific requirements:
+ **DB instance class requirements** – An Aurora global database requires DB instance classes that are optimized for memory\-intensive applications\. For information about the memory optimized DB instance classes, see [DB instance classes](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Concepts.DBInstanceClass.html#Concepts.DBInstanceClass.Types)\. We recommend that you use a db\.r5 or higher instance class\.
+ **AWS Region requirements** – An Aurora global database needs a primary Aurora DB cluster in one AWS Region, and at least one secondary Aurora DB cluster in a different Region\. You can create up to five secondary \(read\-only\) Aurora DB clusters, and each must be in a different Region\. In other words, no two Aurora DB clusters in an Aurora global database can be in the same AWS Region\.
+ **Naming requirements** – The names you choose for each of your Aurora DB clusters must be unique, across all AWS Regions\. You can't use the same name for different Aurora DB clusters even though they're in different Regions\.

Before you can follow the procedures in this section, you need an AWS account\. Complete the setup tasks for working with Amazon Aurora\. For more information, see [Setting up your environment for Amazon Aurora](CHAP_SettingUp_Aurora.md)\. You also need to complete other preliminary steps for creating any Aurora DB cluster\. To learn more, see [Creating an Amazon Aurora DB cluster](Aurora.CreateInstance.md)\. 

## Creating an Amazon Aurora global database<a name="aurora-global-database-creating"></a>

In some cases, you might have an existing Aurora provisioned DB cluster running an Aurora database engine that's global\-compatible\. If so, you can add another AWS Region to it to create your Aurora global database\. To do so, see [Adding an AWS Region to an Amazon Aurora global database](#aurora-global-database-attaching)\. 

To create an Aurora global database by using the AWS Management Console, the AWS CLI, or the RDS API, use the following steps\.

### Console<a name="aurora-global-database-creating.console"></a>

The steps for creating an Aurora global database begin by signing in to an AWS Region that supports the Aurora global database feature\. For a complete list, see [Aurora global databases](Concepts.AuroraFeaturesRegionsDBEngines.grids.md#Concepts.Aurora_Fea_Regions_DB-eng.Feature.GlobalDatabase)\.

One of the steps following is choosing a virtual private cloud \(VPC\) based on Amazon VPC for your Aurora DB cluster\. To use your own VPC, we recommend that you create it in advance so it's available for you to choose\. At the same time, create any related subnets, and as needed a subnet group and security group\. To learn how, see [How to create a VPC for use with Amazon Aurora](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Aurora.CreateVPC.html)\.

For general information about creating an Aurora DB cluster, see [Creating an Amazon Aurora DB cluster](Aurora.CreateInstance.md)\.

**To create an Aurora global database**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose **Create database**\. On the **Create database** page, do the following: 
   + For the database creation method, choose **Standard create**\. \(Don't choose Easy create\.\)
   + For `Engine type` in the **Engine options** section, choose **Amazon Aurora**\.  
![\[Screenshot of some of the engine options on the Create database page.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-db-create.png)

Then choose **Amazon Aurora with MySQL compatibility** or **Amazon Aurora with PostgreSQL compatibility**, and continue creating your Aurora global database by using the steps from the following procedures\.

**Topics**
+ [Creating a global database using Aurora MySQL](#aurora-global-database.create.console.MySQL)
+ [Creating a global database using Aurora PostgreSQL](#aurora-global-database.create.console.PostgreSQL)

#### Creating a global database using Aurora MySQL<a name="aurora-global-database.create.console.MySQL"></a>

The following steps apply to all versions of Aurora MySQL except for Aurora MySQL 5\.6\.10a\. To use Aurora MySQL 5\.6\.10a for your Aurora global database, see [Using Aurora MySQL 5\.6\.10a for an Aurora global database](#aurora-global-database.create.console.MySQL-5.6.10a)\. 

**To create an Aurora global database using Aurora MySQL**

Complete the **Create database** page\.

1. For **Engine options**, choose the following:

   1. For **Edition**, choose **Amazon Aurora with MySQL compatibility**\.

   1. For **Capacity type**, choose **Provisioned**\.

   1. Leave **Replication features** set to the default \(single\-master replication\)\.

   1. Turn on **Show versions that support the global database feature**\.

   1. For **Version**, choose the version of Aurora MySQL that you want to use for your Aurora global database\.  
![\[Screenshot of Edition, Capacity type, Replication features, Engine version, and Version choices when creating an Aurora DB cluster (first phase of Aurora global database).\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-db-create-ams-2.png)

1. For **Templates**, choose **Production**\. Or you can choose Dev/Test if appropriate for your use case\. Don't use Dev/Test in production environments\. 

1. For **Settings**, do the following:

   1. Enter a meaningful name for the DB cluster identifier\. When you finish creating the Aurora global database, this name identifies the primary DB cluster\. 

   1. Enter your own password for the `admin` user account for the DB instance, or have Aurora generate one for you\. If you choose to autogenerate a password, you get an option to copy the password\.  
![\[Screenshot of Settings choices when creating a global database.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-db-create-ams-3.png)

1. For **DB instance class**, choose `db.r5.large` or another memory optimized DB instance class\. We recommend that you use a db\.r5 or higher instance class\.  
![\[Screenshot of DB instance class.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-db-create-apg-3.png)

1. For **Availability & durability**, we recommend that you choose to have Aurora create an Aurora Replica in a different Availability Zone \(AZ\) for you\. If you don't create an Aurora Replica now, you need to do it later\.  
![\[Screenshot of Availability & durability.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-db-create-ams-4b.png)

1. For **Connectivity**, choose the virtual private cloud \(VPC\) based on Amazon VPC that defines the virtual networking environment for this DB instance\. You can choose the defaults to simplify this task\. 

1. Complete the **Database authentication** settings\. To simplify the process, you can choose **Password authentication** now and set up AWS Identity and Access Management \(IAM\) later\.

1. For **Additional configuration**, do the following:

   1. Enter a name for **Initial database name** to create the primary Aurora DB instance for this cluster\. This is the writer node for the Aurora primary DB cluster\. 

      Leave the defaults selected for the DB cluster parameter group and DB parameter group, unless you have your own custom parameter groups that you want to use\. 

   1. Clear the **Enable backtrack** check box if it's selected\. Aurora global databases don't support backtracking\. Otherwise, accept the other default settings for **Additional configuration**\. 

1. Choose **Create database**\. 

   It can take several minutes for Aurora to complete the process of creating the Aurora DB instance, its Aurora Replica, and the Aurora DB cluster\. You can tell when the Aurora DB cluster is ready to use as the primary DB cluster in an Aurora global database by its status\. When that's so, its status and that of the writer and replica node is **Available**, as shown following\.  
![\[Screenshot of Databases with an Aurora DB cluster ready to use for Aurora global database.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-db-create-ams-5.png)

When your primary DB cluster is available, create the Aurora global database by adding a secondary cluster to it\. To do this, follow the steps in [Adding an AWS Region to an Amazon Aurora global database](#aurora-global-database-attaching)\. 

##### Using Aurora MySQL 5\.6\.10a for an Aurora global database<a name="aurora-global-database.create.console.MySQL-5.6.10a"></a>

The following steps apply to the 5\.6\.10a version of Aurora MySQL only\. For other versions of Aurora MySQL, see [Creating a global database using Aurora MySQL](#aurora-global-database.create.console.MySQL)\. 

**To create an Aurora global database using Aurora MySQL 5\.6\.10a**

Complete the **Create database** page\.

1. For **Engine options**, choose the following:

   1. For **Edition**, choose **Amazon Aurora with MySQL compatibility**\.

   1. For **Capacity type**, choose **Provisioned**\.

   1. Leave **Replication features** set to the default \(single\-master replication\)\.

   1. Turn on **Show versions that support the global database feature**\.

   1. For **Version**, choose **Aurora \(MySQL 5\.6\) global\_10a**\. 

1. For **Templates**, choose **Production**\.

1. For **Global database settings**, do the following:

   1. For **Global database identifier**, enter a meaningful name\. 

   1. For **Credentials Settings**, enter your own password for the `postgres` user account for the DB instance, or have Aurora generate one for you\. If you choose Auto generate a password, you get an option to copy the password\.  
![\[Screenshot of Aurora MySQL 5.6.10a Engine options when creating an Aurora global database.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-db-create-ams-5610a-1.png)

1. For **Encryption**, enable or disable encryption as needed\.

1. The remaining sections of the **Create database** page configure the **Primary region settings**\. Complete these as follows:

   1. For **DB instance class**, choose `db.r5.large` or another memory optimized DB instance class\. We recommend that you use a db\.r5 or higher instance class\.   
![\[Screenshot of Aurora MySQL 5.6.10a Primary regions settings section of Create database page.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-db-create-ams-5610a-2.png)

   1. For **Availability & durability**, we recommend that you choose to have Aurora create an Aurora Replica in a different AZ for you\. If you don't create an Aurora Replica now, you need to do it later\. 

   1. For **Connectivity**, choose the virtual private cloud \(VPC\) based on Amazon VPC that defines the virtual networking environment for this DB instance\. You can choose the defaults to simplify this task\. 

   1. For **Encryption key**, choose the key to use\. If you didn't choose **Encryption** earlier, disregard this option\. 

   1. Complete the **Database authentication** settings\. To simplify the process, you can choose **Password authentication** now and set up IAM later\.

   1. For **Additional configuration**, do the following:

      1. For **DB instance identifier**, enter a name for the database instance, or use the default provided\. This is the writer instance for the Aurora primary DB cluster for this Aurora global database\.

      1. For **DB cluster identifier**, enter a meaningful name or accept the default provided\. 

      1. Leave the defaults selected for the DB cluster parameter group and DB parameter group, unless you have your own custom parameter groups that you want to use\. 

      1. You can accept all other default settings for **Additional configuration**\.

   1. Choose **Create database**\. 

      It can take several minutes for Aurora to complete the process of creating the Aurora DB instance, its Aurora Replica, and the Aurora DB cluster\. You can tell when the Aurora DB cluster is ready to use as the primary DB cluster in an Aurora global database by its status\. When that's so, its status and that of the writer and replica node is **Available**, as shown following\.  
![\[Screenshot of Aurora MySQL 5.6.10a–based Aurora global database with one primary DB cluster.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-db-ams5610a-global-database-tree.png)

This Aurora global database still needs a secondary Aurora DB cluster\. You can add that now, by following the steps in [Adding an AWS Region to an Amazon Aurora global database](#aurora-global-database-attaching)\.

#### Creating a global database using Aurora PostgreSQL<a name="aurora-global-database.create.console.PostgreSQL"></a>

**To create an Aurora global database using Aurora PostgreSQL**

Complete the **Create database** page\.

1. For **Engine options**, choose the following:

   1. For **Edition**, choose **Amazon Aurora with PostgreSQL compatibility**\.

   1. For **Capacity type**, choose **Provisioned**\. 

   1. Turn on **Show versions that support the global database feature**\.

   1. For **Version**, choose the version of Aurora PostgreSQL that you want to use for your Aurora global database\.  
![\[Screenshot of Edition, Capacity type, Replication features, Engine version, and Version choices when creating an Aurora DB cluster (first phase of Aurora global database).\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-db-create-apg-1.png)

1. For **Templates**, choose **Production**\. Or you can choose Dev/Test if appropriate\. Don't use Dev/Test in production environments\. 

1. For **Settings**, do the following:

   1. Enter a meaningful name for the DB cluster identifier\. When you finish creating the Aurora global database, this name identifies the primary DB cluster\. 

   1. Enter your own password for the default admin account for the DB cluster, or have Aurora generate one for you\. If you choose Auto generate a password, you get an option to copy the password\.  
![\[Screenshot of Settings choices when creating a global database.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-db-create-apg-2.png)

1. For **DB instance class**, choose `db.r5.large` or another memory optimized DB instance class\. We recommend that you use a db\.r5 or higher instance class\.   
![\[Screenshot of DB instance class.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-db-create-apg-3.png)

1. For **Availability & durability**, we recommend that you choose to have Aurora create an Aurora Replica in a different AZ for you\. If you don't create an Aurora Replica now, you need to do it later\. 

1. For **Connectivity**, choose the virtual private cloud \(VPC\) based on Amazon VPC that defines the virtual networking environment for this DB instance\. You can choose the defaults to simplify this task\. 

1. Complete the **Database authentication** settings\. To simplify the process, you can choose **Password authentication** now and set up IAM or password and Kerberos authentication later\.

1. For **Additional configuration**, do the following:

   1. Enter a name for **Initial database name** to create the primary Aurora DB instance for this cluster\. This is the writer node for the Aurora primary DB cluster\. 

      Leave the defaults selected for the DB cluster parameter group and DB parameter group, unless you have your own custom parameter groups that you want to use\. 

   1. Accept all other default settings for **Additional configuration**, such as Monitoring, Log exports, and so on\.

1. Choose **Create database**\. 

   It can take several minutes for Aurora to complete the process of creating the Aurora DB instance, its Aurora Replica, and the Aurora DB cluster\. When the cluster is ready to use, the Aurora DB cluster and its writer and replica nodes display **Available** status\. This becomes the primary DB cluster of your Aurora global database, after you add a secondary\.  
![\[Screenshot of Databases with an Aurora DB cluster ready to use for Aurora global database.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-db-create-apg-5-add-region.png)

When your primary DB cluster is available, create one or more secondary clusters by following the steps in [Adding an AWS Region to an Amazon Aurora global database](#aurora-global-database-attaching)\. 

### AWS CLI<a name="aurora-global-database-creating.cli"></a>

The AWS CLI commands in the procedures following accomplish the following tasks: 

1. Create an Aurora global database, giving it a name and specifying the Aurora database engine type that you plan to use\. 

1. Create an Aurora DB cluster for the Aurora global database\. 

1. Create the Aurora DB instance for the cluster\.

1. Create an Aurora DB instance for the Aurora DB cluster\.

1. Create a second DB instance for Aurora DB cluster\. This is a reader to complete the Aurora DB cluster\. 

1. Create a second Aurora DB cluster in another Region and then add it to your Aurora global database, by following the steps in [Adding an AWS Region to an Amazon Aurora global database](#aurora-global-database-attaching)\. 

Follow the procedure for your Aurora database engine\.

**Topics**
+ [Creating a global database using Aurora MySQL](#aurora-serverless.create.cli.MySQL)
+ [Creating a global database using Aurora PostgreSQL](#aurora-serverless.create.cli.PostgreSQL)

#### Creating a global database using Aurora MySQL<a name="aurora-serverless.create.cli.MySQL"></a>

**To create an Aurora global database using Aurora MySQL**

1. Use the `[create\-global\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-global-cluster.html)` CLI command, passing the name of the AWS Region, Aurora database engine and version\. Choose your parameters from those shown in the table for the version of Aurora MySQL that you want to use\.

   Other options for Aurora MySQL depend on the version of the Aurora MySQL database engine, as shown in the following table\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-global-database-getting-started.html)

   For Linux, macOS, or Unix:

   ```
   aws rds create-global-cluster --region primary_region  \
       --global-cluster-identifier global_database_id \
       --engine aurora \
       --engine-version version # optional
   ```

   For Windows:

   ```
   aws rds create-global-cluster ^
       --global-cluster-identifier global_database_id ^
       --engine aurora ^
       --engine-version version # optional
   ```

   This creates an "empty" Aurora global database, with just a name \(identifier\) and Aurora database engine\. It can take a few minutes for the Aurora global database to be available\. Before going to the next step, use the `[describe\-global\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-global-clusters.html)` CLI command to see if it's available\.

   ```
   aws rds describe-global-clusters --region primary_region --global-cluster-identifier global_database_id
   ```

   When the Aurora global database is available, you can create its primary Aurora DB cluster\. 

1. To create a primary Aurora DB cluster, use the `[create\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html)` CLI command\. Include the name of your Aurora global database by using the `--global-cluster-identifier`\.

   For Linux, macOS, or Unix:

   ```
   aws rds create-db-cluster \
     --region primary_region \
     --db-cluster-identifier db_cluster_id \
     --master-username userid \
     --master-user-password password \
     --engine { aurora | aurora-mysql } \
     --engine-mode global # Required for --engine-version 5.6.10a only \
     --engine-version version \
     --global-cluster-identifier global_database_id
   ```

   For Windows:

   ```
   aws rds create-db-cluster ^
     --region primary_region ^
     --db-cluster-identifier db_cluster_id ^
     --master-username userid ^
     --master-user-password password ^
     --engine { aurora | aurora-mysql } ^
     --engine-mode global # Required for --engine-version 5.6.10a only ^
     --engine-version version ^
     --global-cluster-identifier global_database_id
   ```

   Other options for Aurora MySQL depend on the version of the Aurora MySQL database engine\. 

   Use the `[describe\-db\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html)` AWS CLI command to confirm that the Aurora DB cluster is ready\. To single out a specific Aurora DB cluster, use `--db-cluster-identifier` parameter\. Or you can leave out the Aurora DB cluster name in the command to get details about all your Aurora DB clusters in the given Region\. 

   ```
   aws rds describe-db-clusters --region primary_region --db-cluster-identifier db_cluster_id
   ```

   When the response shows `"Status": "available"` for the cluster, it's ready to use\.

1. Create the DB instance for your primary Aurora DB cluster\. To do so, use the `[create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html)` CLI command\. Give the command your Aurora DB cluster's name, and specify the configuration details for the instance\. You don't need to pass the `--master-username` and `--master-user-password` parameters in the command, because it gets those from the Aurora DB cluster\.

   For the `--db-instance-class`, you can use only those from the memory optimized classes, such as `db.r5.large`\. We recommend that you use a db\.r5 or higher instance class\. For information about these classes, see [DB instance classes](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Concepts.DBInstanceClass.html#Concepts.DBInstanceClass.Types)\. 

   For Linux, macOS, or Unix:

   ```
   aws rds create-db-instance \
     --db-cluster-identifier db_cluster_id \
     --db-instance-class instance_class \
     --db-instance-identifier db_instance_id \
     --engine { aurora | aurora-mysql} \
     --engine-mode global # Required for --engine-version 5.6.10a only \
     --engine-version version \
     --region primary_region
   ```

   For Windows:

   ```
   aws rds create-db-instance ^
     --db-cluster-identifier db_cluster_id ^
     --db-instance-class instance_class ^
     --db-instance-identifier db_instance_id ^
     --engine { aurora | aurora-mysql } ^
     --engine-mode global # Required for --engine-version 5.6.10a only ^
     --engine-version version ^
     --region primary_region
   ```

   The `create-db-instance` operation might take some time to complete\. Check the status to see if the Aurora DB instance is available before continuing\.

   ```
   aws rds describe-db-clusters --db-cluster-identifier sample_secondary_db_cluster
   ```

   When the command returns a status of "available," you can create another Aurora DB instance for your primary DB cluster\. This is the reader instance \(the Aurora Replica\) for the Aurora DB cluster\.

1. To create another Aurora DB instance for the cluster, use the `[create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html)` CLI command\. 

   For Linux, macOS, or Unix:

   ```
   aws rds create-db-instance \
     --db-cluster-identifier sample_secondary_db_cluster \
     --db-instance-class instance_class \
     --db-instance-identifier  sample_replica_db \
     --engine aurora
   ```

   For Windows:

   ```
   aws rds create-db-instance ^
     --db-cluster-identifier sample_secondary_db_cluster ^
     --db-instance-class instance_class ^
     --db-instance-identifier sample_replica_db ^
     --engine aurora
   ```

When the DB instance is available, replication begins from the writer node to the replica\. Before continuing, check that the DB instance is available with the `[describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html)` CLI command\.

At this point, you have an Aurora global database with its primary Aurora DB cluster containing a writer DB instance and an Aurora Replica\. You can now add a read\-only Aurora DB cluster in a different Region to complete your Aurora global database\. To do so, follow the steps in [Adding an AWS Region to an Amazon Aurora global database](#aurora-global-database-attaching)\. 

#### Creating a global database using Aurora PostgreSQL<a name="aurora-serverless.create.cli.PostgreSQL"></a>

When you create Aurora objects for an Aurora global database by using the following commands, it can take a few minutes for each to become available\. We recommend that after completing any given command, you check the specific Aurora object's status to make sure that the status is available\. 

To do so, use the `[describe\-global\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-global-clusters.html)` CLI command\.

```
aws rds describe-global-clusters --region primary_region 
    --global-cluster-identifier global_database_id
```

**To create an Aurora global database using Aurora PostgreSQL**

1. Use the `[create\-global\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-global-cluster.html)` CLI command\.

   For Linux, macOS, or Unix:

   ```
   aws rds create-global-cluster --region primary_region  \
       --global-cluster-identifier global_database_id \
       --engine aurora-postgresql \
       --engine-version version # optional
   ```

   For Windows:

   ```
   aws rds create-global-cluster ^
       --global-cluster-identifier global_database_id ^
       --engine aurora-postgresql ^
       --engine-version version # optional
   ```

   When the Aurora global database is available, you can create its primary Aurora DB cluster\. 

1. To create a primary Aurora DB cluster, use the `[create\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html)` CLI command\. Include the name of your Aurora global database by using the `--global-cluster-identifier`\.

   For Linux, macOS, or Unix:

   ```
   aws rds create-db-cluster \
     --region primary_region \
     --db-cluster-identifier db_cluster_id \
     --master-username userid \
     --master-user-password password \
     --engine aurora-postgresql \
     --engine-version version \
     --global-cluster-identifier global_database_id
   ```

   For Windows:

   ```
   aws rds create-db-cluster ^
     --region primary_region ^
     --db-cluster-identifier db_cluster_id ^
     --master-username userid ^
     --master-user-password password ^
     --engine aurora-postgresql ^
     --engine-version version ^
     --global-cluster-identifier global_database_id
   ```

   Check that the Aurora DB cluster is ready\. When the response from the following command shows `"Status": "available"` for the Aurora DB cluster, you can continue\. 

   ```
   aws rds describe-db-clusters --region primary_region --db-cluster-identifier db_cluster_id
   ```

1. Create the DB instance for your primary Aurora DB cluster\. To do so, use the `[create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html)` CLI command\. 

   1. Pass the name of your Aurora DB cluster with the `--db-instance-identifier` parameter\.

     You don't need to pass the `--master-username` and `--master-user-password` parameters in the command, because it gets those from the Aurora DB cluster\.

     For the `--db-instance-class`, you can use only those from the memory optimized classes, such as `db.r5.large`\. We recommend that you use a db\.r5 or higher instance class\. For information about these classes, see [DB instance classes](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Concepts.DBInstanceClass.html#Concepts.DBInstanceClass.Types)\. 

     For Linux, macOS, or Unix:

     ```
     aws rds create-db-instance \
       --db-cluster-identifier db_cluster_id \
       --db-instance-class instance_class \
       --db-instance-identifier db_instance_id \
       --engine aurora-postgresql \
       --engine-version version \
       --region primary_region
     ```

     For Windows:

     ```
     aws rds create-db-instance ^
       --db-cluster-identifier db_cluster_id ^
       --db-instance-class instance_class ^
       --db-instance-identifier db_instance_id ^
       --engine aurora-postgresql ^
       --engine-version version ^
       --region primary_region
     ```

1. Check the status of the Aurora DB instance before continuing\.

   ```
   aws rds describe-db-clusters --db-cluster-identifier sample_secondary_db_cluster
   ```

   If the response shows that Aurora DB instance status is "available," you can create another Aurora DB instance for your primary DB cluster\. 

1. To create an Aurora Replica for Aurora DB cluster, use the `[create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html)` CLI command\. 

   For Linux, macOS, or Unix:

   ```
   aws rds create-db-instance \
     --db-cluster-identifier sample_secondary_db_cluster \
     --db-instance-class instance_class \
     --db-instance-identifier  sample_replica_db \
     --engine aurora-postgresql
   ```

   For Windows:

   ```
   aws rds create-db-instance ^
     --db-cluster-identifier sample_secondary_db_cluster ^
     --db-instance-class instance_class ^
     --db-instance-identifier sample_replica_db ^
     --engine aurora-postgresql
   ```

When the DB instance is available, replication begins from the writer node to the replica\. Before continuing, check that the DB instance is available with the `[describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html)` CLI command\.

Your Aurora global database exists, but it has only its primary Region with an Aurora DB cluster made up of a writer DB instance and an Aurora Replica\. You can now add a read\-only Aurora DB cluster in a different Region to complete your Aurora global database\. To do so, follow the steps in [Adding an AWS Region to an Amazon Aurora global database](#aurora-global-database-attaching)\. 

### RDS API<a name="aurora-global-database-creating.api"></a>

 To create an Aurora global database with the RDS API, run the [CreateGlobalCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateGlobalCluster.html) operation\. 

## Adding an AWS Region to an Amazon Aurora global database<a name="aurora-global-database-attaching"></a>

An Aurora global database needs at least one secondary Aurora DB cluster in a different AWS Region than the primary Aurora DB cluster\. You can attach up to five secondary DB clusters to your Aurora global database\. For each secondary DB cluster that you add to your Aurora global database, reduce the number of Aurora Replicas allowed to the primary DB cluster by one\.

For example, if your Aurora global database has 5 secondary Regions, your primary DB cluster can have only 10 \(rather than 15\) Replicas\. For more information, see [Configuration requirements of an Amazon Aurora global database](#aurora-global-database.configuration.requirements)\.

If the combined total of reader instances in the primary cluster and all secondary clusters is 15, you can't add a secondary cluster to an Aurora global database\. 

### Console<a name="aurora-global-database-attach.console"></a>

**To add an AWS Region to an Aurora global database**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane of the AWS Management Console, choose **Databases**\. 

1. Choose the Aurora global database that needs a secondary Aurora DB cluster\. Ensure that the primary Aurora DB cluster is `Available`\.

1.  For **Actions**, choose **Add region**\.   
![\[Screenshot showing provisioned DB cluster with "Add region" chosen from Action menu.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-db-create-apg-5-add-region.png)

1. On the **Add a region** page, choose the secondary AWS Region\. 

   You can't choose an AWS Region that already has a secondary Aurora DB cluster for the same Aurora global database\. Also, it can't be the same Region as the primary Aurora DB cluster\.  
![\[Screenshot showing the "Add a region" pane for an Aurora global database.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-db-create-apg-6-add-region.png)

1. Complete the remaining fields for the secondary Aurora cluster in the new AWS Region\. These are the same configuration options as for any Aurora DB cluster instance, except for the following option for Aurora MySQL–based Aurora global databases only:
   + Enable read replica write forwarding – This optional setting let's your Aurora global database's secondary DB clusters forward write operations to the primary cluster\. For more information, see [Using write forwarding in an Amazon Aurora global database](aurora-global-database-write-forwarding.md)\.   
![\[Screenshot showing the secondary cluster is now part of the Aurora global database.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-database-enable-write-forwarding.png)

1.  **Add region**\. 

After you finish adding the Region to your Aurora global database, you can see it in the list of **Databases** in the AWS Management Console as shown in the screenshot\. 

![\[Screenshot showing the secondary cluster is now part of the Aurora global database.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-db-apg-complete.png)

### AWS CLI<a name="aurora-global-database-attach.cli"></a>

**To add a secondary AWS Region to an Aurora global database**

1. Use the `[create\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html)` CLI command with the name \(`--global-cluster-identifier`\) of your Aurora global database\. For other parameters, do the following: 

1. For `--region`, choose a different AWS Region than that of your Aurora primary Region\.

1. Do one of the following:
   + For an Aurora global database based on Aurora MySQL5\.6\.10a only, use the following parameters:
     + `--engine` – `aurora`
     + `--engine-mode` – `global`
     + `--engine-version` – `5.6.10a`
   + For an Aurora global database based on other Aurora DB engines, choose specific values for the `--engine` and `--engine-version` parameters\. These values are the same as those for the primary Aurora DB cluster in your Aurora global database\. 

     The following table displays current options\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-global-database-getting-started.html)

1. For an encrypted cluster, specify your primary AWS Region as the `--source-region` for encryption\.

The following example creates a new Aurora DB cluster and attaches it to an Aurora global database as a read\-only secondary Aurora DB cluster\. In the last step, an Aurora DB instance is added to the new Aurora DB cluster\.

For Linux, macOS, or Unix:

```
aws rds --region secondary_region \
  create-db-cluster \
    --db-cluster-identifier secondary_cluster_id \
    --global-cluster-identifier global_database_id \
    --engine { aurora | aurora-mysql | aurora-postgresql }
    --engine-version version

aws rds --region secondary_region \
  create-db-instance \
    --db-instance-class instance_class \
    --db-cluster-identifier secondary_cluster_id \
    --db-instance-identifier db_instance_id \
    --engine { aurora | aurora-mysql | aurora-postgresql }
```

For Windows:

```
aws rds --region secondary_region ^
  create-db-cluster ^
    --db-cluster-identifier secondary_cluster_id ^
    --global-cluster-identifier global_database_id_id ^
    --engine { aurora | aurora-mysql | aurora-postgresql } ^
    --engine-version version

aws rds --region secondary_region ^
  create-db-instance ^
    --db-instance-class instance_class ^
    --db-cluster-identifier secondary_cluster_id ^
    --db-instance-identifier db_instance_id ^
    --engine { aurora | aurora-mysql | aurora-postgresql }
```

### RDS API<a name="aurora-global-database-attach.api"></a>

 To add a new AWS Region to an Aurora global database with the RDS API, run the [CreateDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) operation\. Specify the identifier of the existing global database using the `GlobalClusterIdentifier` parameter\. 

## Creating a headless Aurora DB cluster in a secondary Region<a name="aurora-global-database-attach.console.headless"></a>

Although an Aurora global database requires at least one secondary Aurora DB cluster in a different AWS Region than the primary, you can use a *headless* configuration for the secondary cluster\. A headless secondary Aurora DB cluster is one without a DB instance\. This type of configuration can lower expenses for an Aurora global database\. In an Aurora DB cluster, compute and storage are decoupled\. Without the DB instance, you're not charged for compute, only for storage\. If it's set up correctly, a headless secondary's storage volume is kept in\-sync with the primary Aurora DB cluster\. 

You add the secondary cluster as you normally do when creating an Aurora global database\. However, after the primary Aurora DB cluster begins replication to the secondary, you delete the Aurora read\-only DB instance from the secondary Aurora DB cluster\. This secondary cluster is now considered "headless" because it no longer has a DB instance\. Yet, the storage volume is kept in sync with the primary Aurora DB cluster\. 

**Warning**  
 With Aurora PostgreSQL, to create a headless cluster in a secondary AWS Region, use the AWS CLI or RDS API to add the secondary AWS Region\. Skip the step to create the reader DB instance for the secondary cluster\. Currently, creating a headless cluster isn't supported in the RDS Console\. For the CLI and API procedures to use, see [Adding an AWS Region to an Amazon Aurora global database](#aurora-global-database-attaching)\.   
 Creating a reader DB instance in a secondary Region and subsequently deleting it could lead to an Aurora PostgreSQL vacuum issue on the primary Region's writer DB instance\. If you encounter this issue, restart the primary Region's writer DB instance after you delete the secondary Region's reader DB instance\. 

**To add a headless secondary Aurora DB cluster to your Aurora global database**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane of the AWS Management Console, choose **Databases**\. 

1. Choose the Aurora global database that needs a secondary Aurora DB cluster\. Ensure that the primary Aurora DB cluster is `Available`\.

1. For **Actions**, choose **Add region**\. 

1. On the **Add a region** page, choose the secondary AWS Region\. 

   You can't choose an AWS Region that already has a secondary Aurora DB cluster for the same Aurora global database\. Also, it can't be the same Region as the primary Aurora DB cluster\.

1. Complete the remaining fields for the secondary Aurora cluster in the new AWS Region\. These are the same configuration options as for any Aurora DB cluster instance\.

   For an Aurora MySQL–based Aurora global database, disregard the **Enable read replica write forwarding** option\. This option has no function after you delete the reader instance\.

1.  **Add region**\. After you finish adding the Region to your Aurora global database, you can see it in the list of **Databases** in the AWS Management Console as shown in the screenshot\.   
![\[Screenshot showing the secondary cluster with its reader instance is now part of the Aurora global database.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-headless-stage-1.png)

1. Check the status of the secondary Aurora DB cluster and its reader instance before continuing, by using the AWS Management Console or the AWS CLI\. For example:

   ```
   $ aws rds describe-db-clusters --db-cluster-identifier secondary-cluster-id --query '*[].[Status]' --output text
   ```

   It can take several minutes for the status of a newly added secondary Aurora DB cluster to change from `creating` to `available`\. When the Aurora DB cluster is available, you can delete the reader instance\.

1. Select the reader instance in the secondary Aurora DB cluster, and then choose **Delete**\.  
![\[Screenshot showing the reader instance selected and ready to delete.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-headless-stage-2.png)

After deleting the reader instance, the secondary cluster remains part of the Aurora global database\. It has no instance associated with it, as shown following\.

![\[Screenshot showing the headless secondary DB cluster.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-db-headless-secondary.png)

You can use this headless secondary Aurora DB cluster to [manually recover your Amazon Aurora global database from an unplanned outage in the primary AWS Region](aurora-global-database-disaster-recovery.md#aurora-global-database-failover) if such an outage occurs\. 

## Using a snapshot for your Amazon Aurora global database<a name="aurora-global-database.use-snapshot"></a>

You can restore a snapshot of an Aurora DB cluster or from an Amazon RDS DB instance to use as the starting point for your Aurora global database\. You restore the snapshot and create a new Aurora provisioned DB cluster at the same time\. You then add another AWS Region to the restored DB cluster, thus turning it into an Aurora global database\. Any Aurora DB cluster that you create using a snapshot in this way becomes the primary cluster of your Aurora global database\.

The snapshot that you use can be from a `provisioned` or from a `serverless` Aurora DB cluster\. 

**Note**  
You can't create a provisioned Aurora DB cluster from a snapshot made from an Aurora MySQL 5\.6\.10a–based global database\. A snapshot from an Aurora MySQL 5\.6\.10a–based global database can only be restored as an Aurora global database\. 

During the restore process, choose the same DB engine type as the snapshot\. For example, suppose that you want to restore a snapshot that was made from an Aurora Serverless v1 DB cluster running Aurora PostgreSQL\. In this case, you create an Aurora PostgreSQL DB cluster using that same Aurora DB engine and version\. 

The restored DB cluster assumes the role of primary cluster for Aurora global database when you add an AWS Regions to it\. All data contained in this primary cluster is replicated to any secondary clusters that you add to your Aurora global database\.

![\[Screenshot showing the restore snapshot page for an Aurora global database.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-restore-snapshot-01.png)