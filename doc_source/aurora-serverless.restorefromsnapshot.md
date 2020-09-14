# Restoring an Aurora Serverless DB Cluster<a name="aurora-serverless.restorefromsnapshot"></a>

You can configure an Aurora Serverless DB cluster when you restore a provisioned DB cluster snapshot with the AWS Management Console, the AWS CLI, or the RDS API\.

When you restore a snapshot to an Aurora Serverless DB cluster, you can set the following specific values:
+ **Minimum Aurora capacity unit** – Aurora Serverless can reduce capacity down to this capacity unit\.
+ **Maximum Aurora capacity unit** – Aurora Serverless can increase capacity up to this capacity unit\.
+ **Timeout action** – The action to take when a capacity modification times out because it can't find a scaling point\. Aurora can force the capacity change to set the capacity to the specified value as soon as possible\. Or, it can roll back the capacity change to cancel it\. For more information, see [Timeout Action for Capacity Changes](aurora-serverless.how-it-works.md#aurora-serverless.how-it-works.timeout-action)\.
+ **Pause after inactivity** – The amount of time with no database traffic to scale to zero processing capacity\. When database traffic resumes, Aurora automatically resumes processing capacity and scales to handle the traffic\.

For general information about restoring a DB cluster from a snapshot, see [Restoring from a DB Cluster Snapshot](USER_RestoreFromSnapshot.md)\.

## Console<a name="aurora-serverless.restorefromsnapshot.console"></a>

You can restore a DB cluster snapshot to an Aurora DB cluster with the AWS Management Console\.

**To restore a DB cluster snapshot to an Aurora DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the AWS Management Console, choose the AWS Region that hosts your source DB cluster\.

1. In the navigation pane, choose **Snapshots**, and choose the DB cluster snapshot that you want to restore\.

1. For **Actions**, choose **Restore Snapshot**\.

1. On the **Restore DB Cluster** page, choose **Serverless** for **Capacity type**\.  
![\[Restore Aurora Serverless DB cluster with console\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-serverless-restore.png)

1. In the **DB cluster identifier** field, type the name for your restored DB cluster, and complete the other fields\. 

1. In the **Capacity settings** section, modify the scaling configuration\.  
![\[Setting minimum and maximum capacity for an Aurora Serverless DB cluster with console\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-serverless-capacity.png)

1. Choose **Restore DB Cluster**\.

To connect to an Aurora Serverless DB cluster, use the database endpoint\. For details, see the instructions in [Connecting to an Amazon Aurora DB Cluster](Aurora.Connecting.md)\.

**Note**  
If you encounter the following error message, your account requires additional permissions:  
`Unable to create the resource. Verify that you have permission to create service linked role. Otherwise wait and try again later.`  
For more information, see [Using Service\-Linked Roles for Amazon Aurora](UsingWithRDS.IAM.ServiceLinkedRoles.md)\.

## AWS CLI<a name="aurora-serverless.restorefromsnapshot.cli"></a>

To configure an Aurora Serverless DB cluster when you restore from a DB cluster using the AWS CLI, run the [restore\-db\-cluster\-from\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-from-snapshot.html) CLI command and specify `serverless` for the `--engine-mode` option\.

You can optionally specify the `--scaling-configuration` option to configure the minimum capacity, maximum capacity, and automatic pause when there are no connections\. Valid capacity values include the following:
+ Aurora MySQL: `1`, `2`, `4`, `8`, `16`, `32`, `64`, `128`, and `256`\.
+ Aurora PostgreSQL: `2`, `4`, `8`, `16`, `32`, `64`, `192`, and `384`\.

In the following example, you restore from a previously created DB cluster snapshot named *mydbclustersnapshot*\. You restore to a new DB cluster named *mynewdbcluster*\. To restore the DB cluster as an Aurora Serverless DB cluster, set the `--engine-mode` option to `serverless`\. The example also specifies values for the `--scaling-configuration` option\.

For Linux, macOS, or Unix:

```
aws rds restore-db-cluster-from-snapshot \
    --db-cluster-identifier mynewdbcluster \
    --snapshot-identifier mydbclustersnapshot \
    --engine-mode serverless --scaling-configuration MinCapacity=8,MaxCapacity=64,TimeoutAction='ForceApplyCapacityChange',SecondsUntilAutoPause=1000,AutoPause=true
```

For Windows:

```
aws rds restore-db-cluster-from-snapshot ^
    --db-instance-identifier mynewdbcluster ^
    --db-snapshot-identifier mydbclustersnapshot ^
    --engine-mode serverless --scaling-configuration MinCapacity=8,MaxCapacity=64,TimeoutAction='ForceApplyCapacityChange',SecondsUntilAutoPause=1000,AutoPause=true
```

 If you restore a snapshot to an Aurora Serverless cluster with MySQL 5\.7 compatibility, make sure that you add the options `--engine aurora-mysql` and `--engine-version 5.7`\. You can perform such a snapshot restore to create a MySQL 5\.7\-compatible Aurora Serverless cluster from a MySQL 5\.6\-compatible Aurora or Aurora Serverless snapshot\. 

In the following example, you restore from a previously created DB cluster snapshot named *mydbclustersnapshot*\. You restore to a new DB cluster named *mynewdbcluster*\. To restore the DB cluster as an Aurora Serverless DB cluster with MySQL 5\.7 compatibility, specify the `--engine-mode`, `--engine`, and `--engine-version` parameters as follows\.

For Linux, macOS, or Unix:

```
aws rds restore-db-cluster-from-snapshot \
    --db-cluster-identifier mynewdbcluster \
    --snapshot-identifier mydbclustersnapshot \
    --engine-mode serverless --engine aurora-mysql --engine-version 5.7 \
    --scaling-configuration MinCapacity=8,MaxCapacity=64,TimeoutAction='ForceApplyCapacityChange',SecondsUntilAutoPause=1000,AutoPause=true
```

For Windows:

```
aws rds restore-db-cluster-from-snapshot ^
    --db-instance-identifier mynewdbcluster ^
    --db-snapshot-identifier mydbclustersnapshot ^
    --engine-mode serverless --engine aurora-mysql --engine-version 5.7 ^
    --scaling-configuration MinCapacity=8,MaxCapacity=64,TimeoutAction='ForceApplyCapacityChange',SecondsUntilAutoPause=1000,AutoPause=true
```

## RDS API<a name="aurora-serverless.restorefromsnapshot.api"></a>

To configure an Aurora Serverless DB cluster when you restore from a DB cluster using the RDS API, run the [RestoreDBClusterFromSnapshot](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterFromSnapshot.html) operation and specify `serverless` for the `EngineMode` parameter\.

You can optionally specify the `ScalingConfiguration` parameter to configure the minimum capacity, maximum capacity, and automatic pause when there are no connections\. Valid capacity values include the following:
+ Aurora MySQL: `1`, `2`, `4`, `8`, `16`, `32`, `64`, `128`, and `256`\.
+ Aurora PostgreSQL: `2`, `4`, `8`, `16`, `32`, `64`, `192`, and `384`\.