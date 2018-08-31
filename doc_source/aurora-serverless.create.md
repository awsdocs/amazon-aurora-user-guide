# Creating an Aurora Serverless DB Cluster<a name="aurora-serverless.create"></a>

When you create an Aurora Serverless DB cluster, you can set the minimum and maximum capacity for the cluster\. Each capacity unit is equivalent to a specific compute and memory configuration\. Aurora Serverless automatically creates scaling rules for thresholds for CPU utilization, connections, and available memory\. You can also set whether Aurora Serverless pauses the database when there's no activity and then resumes when activity begins again\.

You can set the following specific values:
+ **Minimum Aurora capacity unit** – Aurora Serverless can reduce capacity down to this capacity unit\.
+ **Maximum Aurora capacity unit** – Aurora Serverless can increase capacity up to this capacity unit\.
+ **Pause after inactivity** – The amount of time with no database traffic to scale to zero processing capacity\. When database traffic resumes, Aurora automatically resumes processing capacity and scales to handle the traffic\.

You can create an Aurora Serverless DB cluster with the AWS Management Console, the AWS CLI, or the RDS API\.

For general information about creating a DB cluster, see [Creating an Amazon Aurora DB Cluster](Aurora.CreateInstance.md)\.

## AWS Management Console<a name="aurora-serverless.create.console"></a>

To create a new Aurora Serverless DB cluster with the AWS Management Console, specify **Serverless** for **Capacity type** on the **Specify DB details** page\. 

The following image shows the **Specify DB details** page with **Serverless** chosen during DB cluster creation\.

![\[Create Aurora Serverless DB cluster with console\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-serverless-select.png)

You can configure the scaling configuration of the Aurora Serverless DB cluster by adjusting values in the **Capacity settings** section on the **Configure advanced settings** page\. 

The following image shows the **Capacity settings** you can adjust\.

![\[Setting capacity for an Aurora Serverless DB cluster with console\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-serverless-capacity.png)

For more information on creating an Aurora DB cluster using the console, see [Creating an Amazon Aurora DB Cluster](Aurora.CreateInstance.md)\.

To connect to an Aurora Serverless DB cluster, use the database endpoint\. For details, see the instructions in [Connecting to an Amazon Aurora DB Cluster](Aurora.Connecting.md)\.

**Note**  
If you encounter the following error message, your account requires additional permissions:  
`Unable to create the resource. Verify that you have permission to create service linked role. Otherwise wait and try again later.`  
For more information, see [Using Service\-Linked Roles for Amazon RDS](UsingWithRDS.IAM.ServiceLinkedRoles.md)\.

## AWS CLI<a name="aurora-serverless.create.cli"></a>

To create a new Aurora Serverless DB cluster with the AWS CLI, run the [create\-db\-cluster](http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) command and specify `serverless` for the `--engine-mode` option\.

You can optionally specify the `--scaling-configuration` option to configure the minimum capacity, maximum capacity, and automatic pause when there are no connections\. Valid capacity values are `2`, `4`, `8`, `16`, `32`, `64`, `128`, and `256`\.

The following command creates a new MySQL 5\.6–compatible Serverless DB cluster by setting the `--engine-mode` option to `serverless`\. The example also specifies values for the `--scaling-configuration` option\.

For Linux, OS X, or Unix:

```
aws rds create-db-cluster --db-cluster-identifier sample-cluster --engine aurora --engine-version 5.6.10a \ 
--engine-mode serverless --scaling-configuration MinCapacity=4,MaxCapacity=32,SecondsUntilAutoPause=1000,AutoPause=true \  
--master-username user-name --master-user-password password \
--db-subnet-group-name mysubnetgroup --vpc-security-group-ids sg-c7e5b0d2
```

For Windows:

```
aws rds create-db-cluster --db-cluster-identifier sample-cluster --engine aurora --engine-version 5.6.10a ^
--engine-mode serverless --scaling-configuration MinCapacity=4,MaxCapacity=32,SecondsUntilAutoPause=1000,AutoPause=true ^                       
--master-username user-name --master-user-password password ^
--db-subnet-group-name mysubnetgroup --vpc-security-group-ids sg-c7e5b0d2
```

## RDS API<a name="aurora-serverless.create.api"></a>

To create a new Aurora Serverless DB cluster with the RDS API, run the [CreateDBCluster](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) action and specify `serverless` for the `EngineMode` parameter\.

You can optionally specify the `ScalingConfiguration` parameter to configure the minimum capacity, maximum capacity, and automatic pause when there are no connections\. Valid capacity values are `2`, `4`, `8`, `16`, `32`, `64`, `128`, and `256`\.