# Creating a DB Cluster Snapshot<a name="USER_CreateSnapshotCluster"></a>

Amazon RDS creates a storage volume snapshot of your DB cluster, backing up the entire DB cluster and not just individual databases\. When you create a DB cluster snapshot, you need to identify which DB cluster you are going to back up, and then give your DB cluster snapshot a name so you can restore from it later\. The amount of time it takes to create a DB cluster snapshot varies with the size your databases\. Since the snapshot includes the entire storage volume, the size of files, such as temporary files, also affects the amount of time it takes to create the snapshot\.

You can create a DB cluster snapshot using the AWS Management Console, the AWS CLI, or the RDS API\.

## AWS Management Console<a name="USER_CreateSnapshotCluster.CON"></a>

**To create a DB cluster snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Instances**\.

1. In the list of DB instances, choose the primary instance for the DB cluster\.

1. Choose **Instance actions**, and then choose **Take snapshot**\.

   The **Take DB Snapshot** window appears\.

1. Type the name of the DB cluster snapshot in the **Snapshot name** box\.   
![\[Create snapshot\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/DBClusterSnapshot.png)

1. Choose **Take Snapshot**\.

## CLI<a name="USER_CreateSnapshotCluster.CLI"></a>

When you create a DB cluster snapshot using the AWS CLI, you need to identify which DB cluster you are going to back up, and then give your DB cluster snapshot a name so you can restore from it later\. You can do this by using the AWS CLI [ `create-db-cluster-snapshot`](http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster-snapshot.html) command with the following parameters:
+ `--db-cluster-identifier`
+ `--db-cluster-snapshot-identifier`

In this example, you create a DB cluster snapshot named *mydbclustersnapshot* for a DB cluster called *mydbcluster*\.

**Example**  
For Linux, OS X, or Unix:  

```
1. aws rds create-db-cluster-snapshot /
2.     --db-cluster-identifier mydbcluster /
3.     --db-cluster-snapshot-identifier mydbclustersnapshot
```
For Windows:  

```
1. aws rds create-db-cluster-snapshot ^
2.     --db-cluster-identifier mydbcluster ^
3.     --db-cluster-snapshot-identifier mydbclustersnapshot
```

## API<a name="USER_CreateSnapshotCluster.API"></a>

When you create a DB cluster snapshot using the Amazon RDS API, you need to identify which DB cluster you are going to back up, and then give your DB cluster snapshot a name so you can restore from it later\. You can do this by using the Amazon RDS API [ `CreateDBClusterSnapshot`](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBClusterSnapshot.html) command with the following parameters:
+ DBClusterIdentifier
+ DBClusterSnapshotIdentifier