# Creating an Aurora PostgreSQL cluster with Babelfish<a name="babelfish-create"></a>

You can use Babelfish on Aurora PostgreSQL\. Babelfish is currently supported on Aurora PostgreSQL version 13\.4 and higher\.

You can use the AWS Management Console or the AWS CLI to create an Aurora PostgreSQL cluster with Babelfish\.

**Note**  
In an Aurora PostgreSQL cluster, the `babelfish_db` database name is reserved for Babelfish\. Creating your own "babelfish\_db" database on a Babelfish for Aurora PostgreSQL prevents Aurora from successfully provisioning Babelfish\. 

## Console<a name="babelfish-create-cluster.console"></a>

**To create a cluster with Babelfish running with the AWS Management Console**

1. Open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/), and choose **Create database**\.  
![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/bbf_create_cluster_1.png)

1. For **Choose a database creation method**, do one of the following:
   + To specify detailed engine options, choose **Standard create**\.
   + To use preconfigured options that support best practices for an Aurora cluster, choose **Easy create**\.

1. For **Engine type**, choose **Amazon Aurora**\.

1. For **Edition**, choose **Amazon Aurora PostgreSQL**\.

1. Choose **Show filters**, and then choose **Show versions that support the Babelfish for PostgreSQL feature** to list the engine types that support Babelfish\. Babelfish is currently supported on Aurora PostgreSQL 13\.4 and higher\.

1. For **Available versions**, choose an Aurora PostgreSQL version\.  
![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/bbf_create_cluster_3.png)

1. For **Templates**, choose the template that matches your use case\.

1. For **DB cluster identifier**, enter a name that you can easily find later in the DB cluster list\.

1. For **Master username**, enter an administrator user name\.

   Unlike SQL Server, Babelfish doesn't create an `sa` login\. To create a login named `sa`, enter the name in the **Master username** field\.

   If you don't create a user named `sa` at this time, you can create one later with your choice of client\. After creating the user, use the `ALTER SERVER ROLE` command to add it to `sysadmin`\.

1. For **Master password**, enter a strong password for the administrative user that you just named, and confirm the password\. 

1. For the options that follow, until the **Babelfish settings** section, specify your DB cluster settings\. For information about each setting, see [Settings for Aurora DB clusters](Aurora.CreateInstance.md#Aurora.CreateInstance.Settings)\.

1. To make Babelfish functionality available, select the **Turn on Babelfish** box\.  
![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/bbf_create_cluster_5.png)

1. For **DB cluster parameter group**, do one of the following:
   + Choose **Create new** to create a new parameter group with Babelfish turned on\.
   + Choose **Choose existing** to use an existing parameter group\. If you use an existing group, make sure to modify the group before creating the cluster and add values for Babelfish parameters\. For information about Babelfish parameters, see [Configuring a database for Babelfish](babelfish-configuration.md)\.

     If you use an existing group, provide the group name in the box that follows\.

1. For **Database migration mode**, choose one of the following:
   + **Single database** to migrate a single SQL Server database\.

     In some cases, you might migrate multiple user databases together, with your end goal a complete migration to native Aurora PostgreSQL without Babelfish\. If the final applications require consolidated schemas \(a single `dbo` schema\), make sure to first consolidate your SQL Server databases into a single SQL server database\. Then migrate to Babelfish using **Single database** mode\.
   + **Multiple databases** to migrate multiple SQL Server databases \(originating from a single SQL Server installation\)\. Multiple database mode doesn't consolidate multiple databases that don't originate from a single SQL Server installation\. For information about migrating multiple databases, see [Using Babelfish with a single database or multiple databases](babelfish.md#babelfish-single_vs_multi_db)\.   
![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/bbf_create_cluster_6.png)

1. For **Default collation locale**, enter your server locale\. The default is `en-US`\. For detailed information about collations, see [Babelfish collation support](babelfish-collations.md)\.

1. For **Collation name** field, enter your default collation\. The default is `sql_latin1_general_cp1_ci_as`\. For detailed information, see [Babelfish collation support](babelfish-collations.md)\.

1. For **Babelfish TDS port**, enter the port number for your SQL Server client connect to\. The default is `1433`\.

1. For **DB parameter group**, choose a parameter group or have Aurora create a new group for you with default settings\.

1. For **Failover priority**, choose a failover priority for the instance\. If you don't choose a value, the default is `tier-1`\. This priority determines the order in which replicas are promoted when recovering from a primary instance failure\. For more information, see [Fault tolerance for an Aurora DB cluster](Concepts.AuroraHighAvailability.md#Aurora.Managing.FaultTolerance)\.

1. For **Backup retention period**, choose the length of time \(1–35 days\) that Aurora retains backup copies of the database\. You can use backup copies for point\-in\-time restores \(PITR\) of your database down to the second\. The default retention period is seven days\.  
![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/bbf_create_cluster_7.png)

1. Choose **Copy tags to snapshots** to copy any DB instance tags to a DB snapshot when you create a snapshot\.

1. Choose **Enable encryption** to turn on encryption at rest \(Aurora storage encryption\) for this DB cluster\.

1. Choose **Enable Performance Insights** to turn on Amazon RDS Performance Insights\.

1. Choose **Enable Enhanced monitoring** to start gathering metrics in real time for the operating system that your DB cluster runs on\.

1. Choose **PostgreSQL log** to publish the log files to Amazon CloudWatch Logs\.

1. Choose **Enable auto minor version upgrade** to automatically update your Aurora DB cluster when a minor version upgrade is available\.

1. For **Maintenance window**, do the following:
   + To choose a time for Amazon RDS to make modifications or perform maintenance, choose **Select window**\.
   + To perform Amazon RDS maintenance at an unscheduled time, choose **No preference**\. 

1. Select the **Enable deletion protection** box to protect your database from being deleted by accident\. 

   If you turn on this feature, you can't directly delete the database\. Instead, you need to modify the database cluster and turn off this feature before deleting the database\.  
![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/bbf_create_cluster_9.png)

1. Choose **Create database**\.

You can find your new database set up for Babelfish in the **Databases** listing\. The **Status** column displays **Available** when the deployment is complete\.

![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/bbf_create_cluster_10.png)

## AWS CLI<a name="babelfish-create-cluster.CLI"></a>

Before you create an Aurora DB cluster with Babelfish running using the AWS CLI, make sure to fulfill the required prerequisites, such as creating a parameter group\. For more information, see [DB cluster prerequisites](Aurora.CreateInstance.md#Aurora.CreateInstance.Prerequisites)\.

Before you can use the AWS CLI to create an Aurora PostgreSQL cluster with Babelfish, do the following:
+ Choose your endpoint URL from the list of services at [Amazon Aurora endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/aurora.html)\. 
+ Create a parameter group for the cluster\. For more information about parameter groups, see [Working with DB parameter groups and DB cluster parameter groups](USER_WorkingWithParamGroups.md)\.
+ Modify the parameter group, adding the parameter that turns on Babelfish\.<a name="babelfish.CLI.Creating.DBCluster"></a>

**To create an Aurora PostgreSQL DB cluster with Babelfish using the AWS CLI**

1. Create a parameter group\.

   For Linux, macOS, or Unix:

   ```
   aws rds create-db-cluster-parameter-group \
   --endpoint-url my_endpoint_URL \
   --db-cluster-parameter-group-name my_parameter_group \
   --db-parameter-group-family aurora-postgresql13 \
   --description "parameter_group_description"
   ```

   For Windows:

   ```
   aws rds create-db-cluster-parameter-group ^
   --endpoint-url my_endpoint_URL ^
   --db-cluster-parameter-group-name my_parameter_group ^
   --db-parameter-group-family aurora-postgresql13 ^
   --description "parameter_group_description"
   ```

1. Modify your parameter group to turn on Babelfish\.

   For Linux, macOS, or Unix:

   ```
   aws rds modify-db-cluster-parameter-group \
   --endpoint-url my_endpoint_URL \
   --db-cluster-parameter-group-name my_parameter_group \
   --parameters "ParameterName=rds.babelfish_status,ParameterValue=on,ApplyMethod=pending-reboot"
   ```

   For Windows:

   ```
   aws rds modify-db-cluster-parameter-group ^
   --endpoint-url my_endpoint_URL ^
   --db-cluster-parameter-group-name my_parameter_group ^
   --parameters "ParameterName=rds.babelfish_status,ParameterValue=on,ApplyMethod=pending-reboot"
   ```

1. Identify your DB subnet group and the virtual private cloud \(VPC\) security group ID for your new DB cluster, and then call the [create\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) command\.

   For Linux, macOS, or Unix:

   ```
   aws rds create-db-cluster \
   --db-cluster-identifier my_cluster_name \
   --master-username user_name \
   --master-user-password my_password \
   --engine aurora-postgresql \
   --engine-version 13.4 \
   --vpc-security-group-ids my_security_group \
   --db-subnet-group-name my_subnet_group \
   --db-cluster-parameter-group-name my_parameter_group
   ```

   For Windows:

   ```
   aws rds create-db-cluster ^
   --db-cluster-identifier my_cluster_name ^
   --master-username user_name ^
   --master-user-password my_password ^
   --engine aurora-postgresql ^
   --engine-version 13.4 ^
   --vpc-security-group-ids my_security_group ^
   --db-subnet-group-name my_subnet_group ^
   --db-cluster-parameter-group-name my_parameter_group
   ```

1. Explicitly create the primary instance\. Use the name of the cluster that you created preceding for the value of the `--db-cluster-identifier` option and run the [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) command as shown following\. 

   For Linux, macOS, or Unix:

   ```
   aws rds create-db-instance \
   --db-instance-identifier my_instance_name \
   --db-instance-class db.r5.4xlarge \
   --db-subnet-group-name my_subnet_group \
   --db-cluster-identifier my_cluster_name \
   --engine aurora-postgresql
   ```

   For Windows:

   ```
   aws rds create-db-instance ^
   --db-instance-identifier my_instance_name ^
   --db-instance-class db.r5.4xlarge ^
   --db-subnet-group-name my_subnet_group ^
   --db-cluster-identifier my_cluster_name ^
   --engine aurora-postgresql
   ```