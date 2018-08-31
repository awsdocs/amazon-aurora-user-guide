# Restoring an Aurora Serverless DB Cluster<a name="aurora-serverless.restorefromsnapshot"></a>

You can configure an Aurora Serverless DB cluster when you restore a provisioned DB cluster snapshot with the AWS Management Console, the AWS CLI, or the RDS API\.

For general information about restoring a DB cluster from a snapshot, see [Restoring from a DB Cluster Snapshot](USER_RestoreFromSnapshot.md)\.

## AWS Management Console<a name="aurora-serverless.restorefromsnapshot.console"></a>

You can restore a DB cluster snapshot to an Aurora DB cluster with the AWS Management Console\.

**To restore a DB cluster snapshot to an Aurora DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the upper\-right corner of the AWS Management Console, choose the AWS Region that hosts your source DB cluster\.

1. In the navigation pane, choose **Snapshots**, and choose the DB cluster snapshot that you want to restore\.

1. Choose **Actions**, and then choose **Restore Snapshot**\.

1. On the **Restore DB Cluster** page, choose **Serverless** for **Capacity type**\.  
![\[Restore Aurora Serverless DB cluster with console\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-serverless-restore.png)

1. In the **DB cluster identifier** field, type the name for your restored DB cluster, and complete the other fields\. 

1. Choose **Restore DB Cluster**\.

To connect to an Aurora Serverless DB cluster, use the database endpoint\. For details, see the instructions in [Connecting to an Amazon Aurora DB Cluster](Aurora.Connecting.md)\.

**Note**  
If you encounter the following error message, your account requires additional permissions:  
`Unable to create the resource. Verify that you have permission to create service linked role. Otherwise wait and try again later.`  
For more information, see [Using Service\-Linked Roles for Amazon RDS](UsingWithRDS.IAM.ServiceLinkedRoles.md)\.

## AWS CLI<a name="aurora-serverless.restorefromsnapshot.cli"></a>

To configure an Aurora Serverless DB cluster when you restore from a DB cluster using the AWS CLI, run the [restore\-db\-cluster\-from\-snapshot](http://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-from-snapshot.html) AWS CLI command and specify `serverless` for the `--engine-mode` option\.

You can optionally specify the `--scaling-configuration` option to configure the minimum capacity, maximum capacity, and automatic pause when there are no connections\. Valid capacity values are `2`, `4`, `8`, `16`, `32`, `64`, `128`, and `256`\.

In the following example, you restore from a previously created DB cluster snapshot named *mydbclustersnapshot*\. You restore to a new DB cluster named *mynewdbcluster*\. To restore the DB cluster as an Aurora Serverless DB cluster, set the `--engine-mode` option to `serverless`\. The example also specifies values for the `--scaling-configuration` option\.

For Linux, OS X, or Unix:

```
aws rds restore-db-cluster-from-snapshot \
    --db-cluster-identifier mynewdbcluster \
    --snapshot-identifier mydbclustersnapshot \
    --engine-mode serverless --scaling-configuration MinCapacity=4,MaxCapacity=32,SecondsUntilAutoPause=1000,AutoPause=true
```

For Windows:

```
aws rds restore-db-cluster-from-snapshot ^
    --db-instance-identifier mynewdbcluster ^
    --db-snapshot-identifier mydbclustersnapshot ^
    --engine-mode serverless --scaling-configuration MinCapacity=4,MaxCapacity=32,SecondsUntilAutoPause=1000,AutoPause=true
```

## RDS API<a name="aurora-serverless.restorefromsnapshot.api"></a>

To configure an Aurora Serverless DB cluster when you restore from a DB cluster using the RDS API, run the [RestoreDBClusterFromSnapshot](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterFromSnapshot.html) action and specify `serverless` for the `EngineMode` parameter\.

You can optionally specify the `ScalingConfiguration` parameter to configure the minimum capacity, maximum capacity, and automatic pause when there are no connections\. Valid capacity values are `2`, `4`, `8`, `16`, `32`, `64`, `128`, and `256`\.