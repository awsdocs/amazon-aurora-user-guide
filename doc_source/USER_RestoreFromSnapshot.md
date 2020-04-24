# Restoring from a DB Cluster Snapshot<a name="USER_RestoreFromSnapshot"></a>

Amazon RDS creates a storage volume snapshot of your DB cluster, backing up the entire DB instance and not just individual databases\. You can create a DB cluster by restoring from this DB cluster snapshot\. When you restore the DB cluster, you provide the name of the DB cluster snapshot to restore from, and then provide a name for the new DB cluster that is created from the restore\. You can't restore from a DB cluster snapshot to an existing DB cluster; a new DB cluster is created when you restore\. 

**Note**  
You can't restore a DB cluster from a DB cluster snapshot that is both shared and encrypted\. Instead, you can make a copy of the DB cluster snapshot and restore the DB cluster from the copy\.

## Parameter Group Considerations<a name="USER_RestoreFromSnapshot.Parameters"></a>

We recommend that you retain the parameter group for any DB cluster snapshots you create, so that you can associate your restored DB cluster with the correct parameter group\. You can specify the parameter group when you restore the DB cluster\. 

## Security Group Considerations<a name="USER_RestoreFromSnapshot.Security"></a>

When you restore a DB cluster, the default security group is associated with the restored cluster by default\.

**Note**  
If you're using the Amazon RDS console, you can specify a custom security group to associate with the cluster or create a new VPC security group\.
If you're using the AWS CLI, you can specify a custom security group to associate with the cluster by including the `--vpc-security-group-ids` option in the `restore-db-cluster-from-snapshot` command\.
If you're using the Amazon RDS API, you can include the `VpcSecurityGroupIds.VpcSecurityGroupId.N` parameter in the `RestoreDBClusterFromSnapshot` action\.

As soon as the restore is complete and your new DB cluster is available, you can associate any custom security groups used by the snapshot you restored from\. You must apply these changes by modifying the DB cluster with the RDS console, the AWS CLI `modify-db-cluster` command, or the `ModifyDBCluster` Amazon RDS API operation\. For more information, see [Modifying an Amazon Aurora DB Cluster](Aurora.Modifying.md)\.

## Amazon Aurora Considerations<a name="USER_RestoreFromSnapshot.Aurora"></a>

With Aurora, you restore a DB cluster snapshot to a DB cluster\.

With Aurora MySQL, you can also restore a DB cluster snapshot to an Aurora Serverless DB cluster\. For more information, see [Restoring an Aurora Serverless DB Cluster](aurora-serverless.restorefromsnapshot.md)\.

With Aurora MySQL, you can restore a DB cluster snapshot from a cluster without parallel query to a cluster with parallel query\. Because parallel query is typically used with very large tables, the snapshot mechanism is the fastest way to ingest large volumes of data to an Aurora MySQL parallel query\-enabled cluster\. For more information, see [Working with Parallel Query for Amazon Aurora MySQL](aurora-mysql-parallel-query.md)\.

## Restoring from a Snapshot<a name="USER_RestoreFromSnapshot.Restoring"></a>

You can restore a DB cluster from a DB cluster snapshot using the AWS Management Console, the AWS CLI, or the RDS API\.

### Console<a name="USER_RestoreFromSnapshot.CON"></a>

**To restore a DB cluster from a DB cluster snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Choose the DB cluster snapshot that you want to restore from\. 

1. For **Actions**, choose **Restore Snapshot**\. 

1. On the **Restore DB Instance** page, for **DB Instance Identifier**, enter the name for your restored DB cluster\. 

1. Choose **Restore DB Instance**\. 

### AWS CLI<a name="USER_RestoreFromSnapshot.CLI"></a>

To restore a DB cluster from a DB cluster snapshot, use the AWS CLI command [restore\-db\-cluster\-from\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-from-snapshot.html)\. 

In this example, you restore from a previously created DB cluster snapshot named *mydbclustersnapshot*\. You restore to a new DB cluster named *mynewdbcluster*\. 

**Example**  
For Linux, macOS, or Unix:  

```
1. aws rds restore-db-cluster-from-snapshot \
2.     --db-cluster-identifier mynewdbcluster \
3.     --snapshot-identifier mydbclustersnapshot \
4.     --engine aurora|aurora-postgresql
```
For Windows:  

```
1. aws rds restore-db-cluster-from-snapshot ^
2.     --db-cluster-identifier mynewdbcluster ^
3.     --snapshot-identifier mydbclustersnapshot ^
4.     --engine aurora|aurora-postgresql
```

After the DB cluster has been restored, you must add the DB cluster to the security group used by the DB cluster used to create the DB cluster snapshot if you want the same functionality as that of the previous DB cluster\.

**Important**  
If you use the console to restore a DB cluster, then Amazon RDS automatically creates the primary instance \(writer\) for your DB cluster\. If you use the AWS CLI to restore a DB cluster, you must explicitly create the primary instance for your DB cluster\. The primary instance is the first instance that is created in a DB cluster\. Call the [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) AWS CLI command to create the primary instance for your DB cluster\. Include the name of the DB cluster as the `--db-cluster-identifier` option value\.

### RDS API<a name="USER_RestoreFromSnapshot.API"></a>

To restore a DB cluster from a DB cluster snapshot, call the RDS API operation [RestoreDBClusterFromSnapshot](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterFromSnapshot.html) with the following parameters: 
+ `DBClusterIdentifier` 
+ `SnapshotIdentifier` 

**Important**  
If you use the console to restore a DB cluster, then Amazon RDS automatically creates the primary instance \(writer\) for your DB cluster\. If you use the RDS API to restore a DB cluster, you must explicitly create the primary instance for your DB cluster\. The primary instance is the first instance that is created in a DB cluster\. Call the RDS API operation [ CreateDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBInstance.html) to create the primary instance for your DB cluster\. Include the name of the DB cluster as the `DBClusterIdentifier` parameter value\.