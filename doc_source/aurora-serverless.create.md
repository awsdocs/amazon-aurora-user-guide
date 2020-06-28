# Creating an Aurora Serverless DB Cluster<a name="aurora-serverless.create"></a>

When you create an Aurora Serverless DB cluster, you can set the minimum and maximum capacity for the cluster\. Each capacity unit is equivalent to a specific compute and memory configuration\. Aurora Serverless automatically creates scaling rules for thresholds for CPU utilization, connections, and available memory\. You can also set whether Aurora Serverless pauses the database when there's no activity and then resumes when activity begins again\.

You can set the following specific values:
+ **Minimum Aurora capacity unit** – Aurora Serverless can reduce capacity down to this capacity unit\.
+ **Maximum Aurora capacity unit** – Aurora Serverless can increase capacity up to this capacity unit\.
+ **Timeout action** – The action to take when a capacity modification times out because it can't find a scaling point\. Aurora can force the capacity change to set the capacity to the specified value as soon as possible\. Or, it can roll back the capacity change to cancel it\. For more information, see [Timeout Action for Capacity Changes](aurora-serverless.how-it-works.md#aurora-serverless.how-it-works.timeout-action)\.
+ **Pause after inactivity** – The amount of time with no database traffic to scale to zero processing capacity\. When database traffic resumes, Aurora automatically resumes processing capacity and scales to handle the traffic\.

You can create an Aurora Serverless DB cluster with the AWS Management Console, the AWS CLI, or the RDS API\.

For general information about creating a DB cluster, see [Creating an Amazon Aurora DB Cluster](Aurora.CreateInstance.md)\.

**Note**  
 Currently, Aurora Serverless isn't available in all AWS Regions\. For more information on Aurora Serverless, see  [Pricing](https://aws.amazon.com/rds/aurora/pricing/) under either **MySQL\-Compatible Edition** or **PostgreSQL\-Compatible Edition**\.   
 The cluster volume for an Aurora Serverless cluster is always encrypted\. You can choose the encryption key, but not turn off encryption\. Therefore, you can't perform operations that aren't allowed for encrypted snapshots\. For example, you can't copy snapshots of Aurora Serverless clusters to a different AWS Region\. 

## Console<a name="aurora-serverless.create.console"></a>

To create a new Aurora Serverless DB cluster with the AWS Management Console, specify **Serverless** for **Capacity type** on the **Specify DB details** page\. 

### Example for Aurora MySQL<a name="aurora-serverless.create.console.MySQL"></a>

The following image shows the **Specify DB details** page with **Serverless** chosen during DB cluster creation for the Aurora MySQL engine\. 

![\[Create Aurora Serverless DB cluster with console\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-serverless-select.png)

You can configure the scaling configuration of the Aurora Serverless DB cluster by adjusting values in the **Capacity settings** section on the **Configure advanced settings** page\. 

The following image shows the **Capacity settings** you can adjust for an Aurora MySQL Serverless DB cluster\.

![\[Setting capacity for an Aurora MySQL Serverless DB cluster with console\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-serverless-capacity.png)

You can also enable the Data API for your Aurora MySQL Serverless DB cluster\. For more information, see [Using the Data API for Aurora Serverless](data-api.md)\. 

### Example for Aurora PostgreSQL<a name="aurora-serverless.create.console.PostgreSQL"></a>

For the Aurora PostgreSQL engine, first choose the **DB engine version** for Aurora PostgreSQL that is compatible with PostgreSQL version 10\.7\. Then choose **Serverless** for the **Capacity type**\.

![\[Create Aurora PostgreSQL Serverless DB cluster with console\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-serverless-select-postgres.png)

You can configure the scaling configuration of the Aurora Serverless DB cluster by adjusting values in the **Capacity settings** section on the **Configure advanced settings** page\. 

The following image shows the **Capacity settings** you can adjust for an Aurora PostgreSQL Serverless DB cluster\.

![\[Setting capacity for an Aurora PostgreSQL Serverless DB cluster with console\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-serverless-capacity-postgres.png)

For more information on creating an Aurora DB cluster using the AWS Management Console, see [Creating an Amazon Aurora DB Cluster](Aurora.CreateInstance.md)\.

To connect to an Aurora Serverless DB cluster, use the database endpoint\. For details, see the instructions in [Connecting to an Amazon Aurora DB Cluster](Aurora.Connecting.md)\.

**Note**  
If you encounter the following error message, your account requires additional permissions:  
`Unable to create the resource. Verify that you have permission to create service linked role. Otherwise wait and try again later.`  
For more information, see [Using Service\-Linked Roles for Amazon Aurora](UsingWithRDS.IAM.ServiceLinkedRoles.md)\.

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