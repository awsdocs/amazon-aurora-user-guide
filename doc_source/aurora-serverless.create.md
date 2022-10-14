# Creating an Aurora Serverless v1 DB cluster<a name="aurora-serverless.create"></a>

 The following procedure creates an Aurora Serverless v1 cluster without any of your schema objects or data\. If you want to create an Aurora Serverless v1 cluster that's a duplicate of an existing provisioned or Aurora Serverless v1 cluster, you can perform a snapshot restore or cloning operation instead\. For those details, see [Restoring from a DB cluster snapshot](aurora-restore-snapshot.md) and [Cloning a volume for an Amazon Aurora DB cluster](Aurora.Managing.Clone.md)\. You can't convert an existing provisioned cluster to Aurora Serverless v1\. You also can't convert an existing Aurora Serverless v1 cluster back to a provisioned cluster\. 

 When you create an Aurora Serverless v1 DB cluster, you can set the minimum and maximum capacity for the cluster\. A capacity unit is equivalent to a specific compute and memory configuration\. Aurora Serverless v1 creates scaling rules for thresholds for CPU utilization, connections, and available memory and seamlessly scales to a range of capacity units as needed for your applications\. For more information see [Aurora Serverless v1 architecture](aurora-serverless-v1.how-it-works.md#aurora-serverless.architecture)\. 

 You can set the following specific values for your Aurora Serverless v1 DB cluster: 
+  **Minimum Aurora capacity unit** – Aurora Serverless v1 can reduce capacity down to this capacity unit\. 
+  **Maximum Aurora capacity unit** – Aurora Serverless v1 can increase capacity up to this capacity unit\. 

 You can also choose the following optional scaling configuration options: 
+  **Force scaling the capacity to the specified values when the timeout is reached** – You can choose this setting if you want Aurora Serverless v1 to force Aurora Serverless v1 to scale even if it can't find a scaling point before it times out\. If you want Aurora Serverless v1 to cancel capacity changes if it can't find a scaling point, don't choose this setting\. For more information, see [Timeout action for capacity changes](aurora-serverless-v1.how-it-works.md#aurora-serverless.how-it-works.timeout-action)\. 
+  **Pause compute capacity after consecutive minutes of inactivity** – You can choose this setting if you want Aurora Serverless v1 to scale to zero when there's no activity on your DB cluster for an amount of time you specify\. With this setting enabled, your Aurora Serverless v1 DB cluster automatically resumes processing and scales to the necessary capacity to handle the workload when database traffic resumes\. To learn more, see [Pause and resume for Aurora Serverless v1](aurora-serverless-v1.how-it-works.md#aurora-serverless.how-it-works.pause-resume)\. 

 Before you can create an Aurora Serverless v1 DB cluster, you need an AWS account\. You also need to have completed the setup tasks for working with Amazon Aurora\. For more information, see [Setting up your environment for Amazon Aurora](CHAP_SettingUp_Aurora.md)\. You also need to complete other preliminary steps for creating any Aurora DB cluster\. To learn more, see [Creating an Amazon Aurora DB cluster](Aurora.CreateInstance.md)\. 

 Aurora Serverless v1 is available in certain AWS Regions and for specific Aurora MySQL and Aurora PostgreSQL versions only\. For more information, see [Aurora Serverless v1](Concepts.Aurora_Fea_Regions_DB-eng.Feature.ServerlessV1.md)\. 

**Note**  
 The cluster volume for an Aurora Serverless v1 cluster is always encrypted\. When you create your Aurora Serverless v1 DB cluster, you can't turn off encryption, but you can choose to use your own encryption key\. With Aurora Serverless v2, you can choose whether to encrypt the cluster volume\. 

 You can create an Aurora Serverless v1 DB cluster with the AWS Management Console, the AWS CLI, or the RDS API\.

**Note**  
If you receive the following error message when trying to create your cluster, your account needs additional permissions\.   
`Unable to create the resource. Verify that you have permission to create service linked role. Otherwise wait and try again later.`  
See [Using service\-linked roles for Amazon Aurora](UsingWithRDS.IAM.ServiceLinkedRoles.md) for more information\.

You can't directly connect to the DB instance on your Aurora Serverless v1 DB cluster\. To connect to your Aurora Serverless v1 DB cluster, you use the database endpoint\. You can find the endpoint for your Aurora Serverless v1 DB cluster on the **Connectivity & security** tab for your cluster in the AWS Management Console\. For more information, see [Connecting to an Amazon Aurora DB cluster](Aurora.Connecting.md)\.

## Console<a name="aurora-serverless.create.console"></a>

Use the following general procedure\. For more information on creating an Aurora DB cluster using the AWS Management Console, see [Creating an Amazon Aurora DB cluster](Aurora.CreateInstance.md)\.

**To create a new Aurora Serverless v1 DB cluster**

1. Sign in to the AWS Management Console\.

1. Choose an AWS Region that supports Aurora Serverless v1\.

1. Choose Amazon RDS from the AWS Services list\.

1. Choose **Create database**\. 

1. On the **Create database** page:

   1. Choose **Standard Create** for the database creation method\.

   1. Choose **Amazon Aurora** for the Engine type in the **Engine options** section\.

   1. Choose **Amazon Aurora with MySQL compatibility** or **Amazon Aurora with PostgreSQL compatibility**, and continue creating the Aurora Serverless v1 DB cluster by using the steps from the following examples\.

      If you choose a version of the DB engine that doesn't support Aurora Serverless v1, the **Serverless** option doesn't display\.

### Example for Aurora MySQL<a name="aurora-serverless.create.console.MySQL"></a>

Use the following procedure\.

**To create an Aurora Serverless v1 DB cluster for Aurora MySQL**

1.  Choose **Amazon Aurora with MySQL Compatibility** for the Edition\.

1. Choose the Aurora MySQL version you want for your DB cluster\. The supported versions are shown on the right side of the page\.  
![\[Create Aurora Serverless v1 DB cluster with console\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-serverless-select.png)

1. For **DB instance class**, confirm that **Serverless** is chosen\.

1. Set the **Capacity range** for the DB cluster\.

1. Adjust values as needed in the **Additional scaling configuration** section of the page\. To learn more about capacity settings, see [Autoscaling for Aurora Serverless v1](aurora-serverless-v1.how-it-works.md#aurora-serverless.how-it-works.auto-scaling)\.  
![\[Setting capacity for an Aurora MySQL Serverless v1 DB cluster with console\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-serverless-capacity.png)

1. To enable the Data API for your Aurora Serverless v1 DB cluster, select the **Data API** check box under **Additional configuration** in the **Connectivity** section\.

   To learn more about the Data API, see [Using the Data API for Aurora Serverless v1](data-api.md)\.

1. Choose other database settings as needed, then choose **Create database\.**

### Example for Aurora PostgreSQL<a name="aurora-serverless.create.console.PostgreSQL"></a>

Use the following procedure\.

**To create an Aurora Serverless v1 DB cluster for Aurora PostgreSQL**

1.  Choose **Amazon Aurora with Postgres; Compatibility** for the Edition\.

1. Choose the Aurora PostgreSQL version you want for your DB cluster\. The supported versions are shown on the right side of the page\.  
![\[Create Aurora PostgreSQL Serverless v1 DB cluster with console\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-serverless-select-postgres.png)

1. For **DB instance class**, confirm that **Serverless** is chosen\.

1. Set the **Capacity range** for the DB cluster\.

1. Adjust values as needed in the **Additional scaling configuration** section of the page\. To learn more about capacity settings, see [Autoscaling for Aurora Serverless v1](aurora-serverless-v1.how-it-works.md#aurora-serverless.how-it-works.auto-scaling)\.  
![\[Setting capacity for an Aurora PostgreSQL Serverless v1 DB cluster with console\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-serverless-capacity-postgres.png)

1. To enable the Data API for your Aurora Serverless v1 DB cluster, select the **Data API** check box under **Additional configuration** in the **Connectivity** section\.

   To learn more about the Data API, see [Using the Data API for Aurora Serverless v1](data-api.md)\.

1. Choose other database settings as needed, then choose **Create database\.**

## AWS CLI<a name="aurora-serverless.create.cli"></a>

 To create a new Aurora Serverless v1 DB cluster with the AWS CLI, run the [create\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) command and specify `serverless` for the `--engine-mode` option\. 

 You can optionally specify the `--scaling-configuration` option to configure the minimum capacity, maximum capacity, and automatic pause when there are no connections\. 

 The following command examples create a new Serverless DB cluster by setting the `--engine-mode` option to `serverless`\. The examples also specify values for the `--scaling-configuration` option\. 

### Example for Aurora MySQL<a name="aurora-serverless.create.cli.MySQL"></a>

 The following commands create new MySQL–compatible Serverless DB clusters\. Valid capacity values for Aurora MySQL are `1`, `2`, `4`, `8`, `16`, `32`, `64`, `128`, and `256`\. 

For Linux, macOS, or Unix:

```
aws rds create-db-cluster --db-cluster-identifier sample-cluster --engine aurora --engine-version 5.6.mysql_aurora.1.22.3 \
--engine-mode serverless --scaling-configuration MinCapacity=4,MaxCapacity=32,SecondsUntilAutoPause=1000,AutoPause=true \
--master-username username --master-user-password password

aws rds create-db-cluster --db-cluster-identifier sample-cluster --engine aurora-mysql --engine-version 5.7.mysql_aurora.2.08.3 \
--engine-mode serverless --scaling-configuration MinCapacity=4,MaxCapacity=32,SecondsUntilAutoPause=1000,AutoPause=true \
--master-username username --master-user-password password
```

For Windows:

```
aws rds create-db-cluster --db-cluster-identifier sample-cluster --engine aurora --engine-version 5.6.mysql_aurora.1.22.3 ^
--engine-mode serverless --scaling-configuration MinCapacity=4,MaxCapacity=32,SecondsUntilAutoPause=1000,AutoPause=true ^
--master-username username --master-user-password password

aws rds create-db-cluster --db-cluster-identifier sample-cluster --engine aurora-mysql --engine-version 5.7.mysql_aurora.2.08.3 ^
--engine-mode serverless --scaling-configuration MinCapacity=4,MaxCapacity=32,SecondsUntilAutoPause=1000,AutoPause=true ^
--master-username username --master-user-password password
```

### Example for Aurora PostgreSQL<a name="aurora-serverless.create.cli.PostgreSQL"></a>

 The following command creates a new PostgreSQL 10\.12–compatible Serverless DB cluster\. Valid capacity values for Aurora PostgreSQL are `2`, `4`, `8`, `16`, `32`, `64`, `192`, and `384`\. 

For Linux, macOS, or Unix:

```
aws rds create-db-cluster --db-cluster-identifier sample-cluster --engine aurora-postgresql --engine-version 10.12 \
--engine-mode serverless --scaling-configuration MinCapacity=8,MaxCapacity=64,SecondsUntilAutoPause=1000,AutoPause=true \
--master-username username --master-user-password password
```

For Windows:

```
aws rds create-db-cluster --db-cluster-identifier sample-cluster --engine aurora-postgresql --engine-version 10.12 ^
--engine-mode serverless --scaling-configuration MinCapacity=8,MaxCapacity=64,SecondsUntilAutoPause=1000,AutoPause=true ^
--master-username username --master-user-password password
```

## RDS API<a name="aurora-serverless.create.api"></a>

 To create a new Aurora Serverless v1 DB cluster with the RDS API, run the [CreateDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) operation and specify `serverless` for the `EngineMode` parameter\. 

 You can optionally specify the `ScalingConfiguration` parameter to configure the minimum capacity, maximum capacity, and automatic pause when there are no connections\. Valid capacity values include the following: 
+  Aurora MySQL: `1`, `2`, `4`, `8`, `16`, `32`, `64`, `128`, and `256`\. 
+  Aurora PostgreSQL: `2`, `4`, `8`, `16`, `32`, `64`, `192`, and `384`\. 