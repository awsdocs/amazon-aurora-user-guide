# Creating an Aurora Serverless DB cluster<a name="aurora-serverless.create"></a>

When you create an Aurora Serverless DB cluster, you can set the minimum and maximum capacity for the cluster\. Each capacity unit is equivalent to a specific compute and memory configuration\. Aurora Serverless automatically creates scaling rules for thresholds for CPU utilization, connections, and available memory\. You can also set whether Aurora Serverless pauses the database when there's no activity and then resumes when activity begins again\.

You can set the following specific values:
+ **Minimum Aurora capacity unit** – Aurora Serverless can reduce capacity down to this capacity unit\.
+ **Maximum Aurora capacity unit** – Aurora Serverless can increase capacity up to this capacity unit\.
+ **Timeout action** – The action to take when a capacity modification times out because it can't find a scaling point\. Aurora can force the capacity change to set the capacity to the specified value as soon as possible\. Or, it can roll back the capacity change to cancel it\. For more information, see [Timeout action for capacity changes](aurora-serverless.how-it-works.md#aurora-serverless.how-it-works.timeout-action)\.
+ **Pause after inactivity** – The amount of time with no database traffic to scale to zero processing capacity\. When database traffic resumes, Aurora automatically resumes processing capacity and scales to handle the traffic\.

Before you can create an Aurora Serverless DB cluster, you need to have an AWS account and you need to have completed the setup tasks for working with Amazon Aurora\. For more information, see [Setting up your environment for Amazon Aurora](CHAP_SettingUp_Aurora.md)\. You also need to complete other preliminary steps for creating any Aurora DB cluster\. To learn more, see [Creating an Amazon Aurora DB cluster](Aurora.CreateInstance.md)\. 

**Note**  
 Aurora Serverless is available in certain AWS Regions only\. Currently, Aurora Serverless requires Aurora \(MySQL\) 5\.6\.10a, Aurora \(MySQL 5\.7\) 2\.07\.1, or Aurora PostgreSQL \(compatible with PostgreSQL 10\.7\)\. 

You can create an Aurora Serverless DB cluster with the AWS Management Console, the AWS CLI, or the RDS API\.

## Console<a name="aurora-serverless.create.console"></a>

To create a new Aurora Serverless DB cluster, you sign in to the AWS Management Console\. Choose an AWS Region that supports Aurora Serverless\. Choose Amazon RDS from the AWS Services list, and then click **Create database**\.

On the **Create database** page: 
+ Choose **Standard Create** for the database creation method\. 
+ Choose **Amazon Aurora** for the Engine type in the **Engine options** section\. 

You can now choose either **Amazon Aurora with MySQL compatibility** or **Amazon Aurora with PostgreSQL compatibility**\. You can continue setting up your Aurora Serverless DB cluster by using the steps for your choice as outlined in the following\. If you choose a version of the DB engine that doesn't support Aurora Serverless, the **Serverless** option doesn't display\. 

### Example for Aurora MySQL<a name="aurora-serverless.create.console.MySQL"></a>

Choose **Amazon Aurora with MySQL Compatibility** for the Edition\. Choose the Aurora MySQL engine you want for your cluster from the **Version** selector\. The following image shows the versions available for selection\. 

![\[Create Aurora Serverless DB cluster with console\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-serverless-select.png)

Choose **Serverless** for the Capacity type\. 

You can configure the scaling configuration of the Aurora Serverless DB cluster by adjusting values in the **Capacity settings** section of the page\. To learn more about capacity settings, see [Autoscaling for Aurora Serverless](aurora-serverless.how-it-works.md#aurora-serverless.how-it-works.auto-scaling)\. The following image shows the **Capacity settings** you can adjust for an Aurora MySQL Serverless DB cluster\.

![\[Setting capacity for an Aurora MySQL Serverless DB cluster with console\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-serverless-capacity.png)

You can also enable the Data API for your Aurora MySQL Serverless DB cluster\. Select the **Data API** checkbox in the **Connectivity** section of the Create database page\. See [Using the Data API for Aurora Serverless](data-api.md) for more information about the Data API\. 

### Example for Aurora PostgreSQL<a name="aurora-serverless.create.console.PostgreSQL"></a>

Choose **Amazon Aurora with Postgres; Compatibility** for the Edition\. Then choose the **Version** of Aurora PostgreSQL that is compatible with PostgreSQL version 10\.7, as shown in the following image\. 

![\[Create Aurora PostgreSQL Serverless DB cluster with console\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-serverless-select-postgres.png)

You can configure the scaling configuration of the Aurora Serverless DB cluster by adjusting values in the **Capacity settings** section of the page\. The following image shows the **Capacity settings** you can adjust for an Aurora PostgreSQL Serverless DB cluster\. To learn more about capacity settings, see [Autoscaling for Aurora Serverless](aurora-serverless.how-it-works.md#aurora-serverless.how-it-works.auto-scaling)\. 

![\[Setting capacity for an Aurora PostgreSQL Serverless DB cluster with console\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-serverless-capacity-postgres.png)

You can also enable the Data API for your Aurora MySQL Serverless DB cluster\. Select the **Data API** checkbox in the **Connectivity** section of the Create database page\. See [Using the Data API for Aurora Serverless](data-api.md) for more information about the Data API\. 

For more information on creating an Aurora DB cluster using the AWS Management Console, see [Creating an Amazon Aurora DB cluster](Aurora.CreateInstance.md)\.

To connect to an Aurora Serverless DB cluster, use the database endpoint\. To learn how, see [Connecting to an Amazon Aurora DB cluster](Aurora.Connecting.md)\.

**Note**  
If you receive the following error message when trying to create your cluster, your account needs additional permissions\.   
`Unable to create the resource. Verify that you have permission to create service linked role. Otherwise wait and try again later.`  
See [Using service\-linked roles for Amazon Aurora](UsingWithRDS.IAM.ServiceLinkedRoles.md) for more information\.

## AWS CLI<a name="aurora-serverless.create.cli"></a>

To create a new Aurora Serverless DB cluster with the AWS CLI, run the [create\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) command and specify `serverless` for the `--engine-mode` option\.

You can optionally specify the `--scaling-configuration` option to configure the minimum capacity, maximum capacity, and automatic pause when there are no connections\. 

The following command examples create a new Serverless DB cluster by setting the `--engine-mode` option to `serverless`\. The examples also specify values for the `--scaling-configuration` option\.

### Example for Aurora MySQL<a name="aurora-serverless.create.cli.MySQL"></a>

The following commands create new MySQL–compatible Serverless DB clusters\. Valid capacity values for Aurora MySQL are `1`, `2`, `4`, `8`, `16`, `32`, `64`, `128`, and `256`\.

For Linux, macOS, or Unix:

```
aws rds create-db-cluster --db-cluster-identifier sample-cluster --engine aurora --engine-version 5.6.10a \
--engine-mode serverless --scaling-configuration MinCapacity=4,MaxCapacity=32,SecondsUntilAutoPause=1000,AutoPause=true \
--master-username username --master-user-password password

aws rds create-db-cluster --db-cluster-identifier sample-cluster --engine aurora-mysql --engine-version 5.7.mysql_aurora.2.07.1 \
--engine-mode serverless --scaling-configuration MinCapacity=4,MaxCapacity=32,SecondsUntilAutoPause=1000,AutoPause=true \
--master-username username --master-user-password password
```

For Windows:

```
aws rds create-db-cluster --db-cluster-identifier sample-cluster --engine aurora --engine-version 5.6.10a ^
--engine-mode serverless --scaling-configuration MinCapacity=4,MaxCapacity=32,SecondsUntilAutoPause=1000,AutoPause=true ^
--master-username username --master-user-password password

aws rds create-db-cluster --db-cluster-identifier sample-cluster --engine aurora-mysql --engine-version 5.7.mysql_aurora.2.07.1 ^
--engine-mode serverless --scaling-configuration MinCapacity=4,MaxCapacity=32,SecondsUntilAutoPause=1000,AutoPause=true ^
--master-username username --master-user-password password
```

### Example for Aurora PostgreSQL<a name="aurora-serverless.create.cli.PostgreSQL"></a>

The following command creates a new PostgreSQL 10\.7–compatible Serverless DB cluster\. Valid capacity values for Aurora PostgreSQL are `2`, `4`, `8`, `16`, `32`, `64`, `192`, and `384`\.

For Linux, macOS, or Unix:

```
aws rds create-db-cluster --db-cluster-identifier sample-cluster --engine aurora-postgresql --engine-version 10.7 \
--engine-mode serverless --scaling-configuration MinCapacity=8,MaxCapacity=64,SecondsUntilAutoPause=1000,AutoPause=true \
--master-username username --master-user-password password
```

For Windows:

```
aws rds create-db-cluster --db-cluster-identifier sample-cluster --engine aurora-postgresql --engine-version 10.7 ^
--engine-mode serverless --scaling-configuration MinCapacity=8,MaxCapacity=64,SecondsUntilAutoPause=1000,AutoPause=true ^
--master-username username --master-user-password password
```

## RDS API<a name="aurora-serverless.create.api"></a>

To create a new Aurora Serverless DB cluster with the RDS API, run the [CreateDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) operation and specify `serverless` for the `EngineMode` parameter\.

You can optionally specify the `ScalingConfiguration` parameter to configure the minimum capacity, maximum capacity, and automatic pause when there are no connections\. Valid capacity values include the following:
+ Aurora MySQL: `1`, `2`, `4`, `8`, `16`, `32`, `64`, `128`, and `256`\.
+ Aurora PostgreSQL: `2`, `4`, `8`, `16`, `32`, `64`, `192`, and `384`\.