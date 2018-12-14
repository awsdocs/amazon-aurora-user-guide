# Restoring from a DB Cluster Snapshot<a name="USER_RestoreFromSnapshot"></a>

Amazon RDS creates a storage volume snapshot of your DB cluster, backing up the entire DB instance and not just individual databases\. You can create a DB cluster by restoring from this DB cluster snapshot\. When you restore the DB cluster, you provide the name of the DB cluster snapshot to restore from, and then provide a name for the new DB cluster that is created from the restore\. You can't restore from a DB cluster snapshot to an existing DB cluster; a new DB cluster is created when you restore\. 

**Note**  
You can't restore a DB cluster from a DB cluster snapshot that is both shared and encrypted\. Instead, you can make a copy of the DB cluster snapshot and restore the DB cluster from the copy\.

## Parameter Group Considerations<a name="USER_RestoreFromSnapshot.Parameters"></a>

We recommend that you retain the parameter group for any DB cluster snapshots you create, so that you can associate your restored DB cluster with the correct parameter group\. You can specify the parameter group when you restore the DB cluster\. 

## Security Group Considerations<a name="USER_RestoreFromSnapshot.Security"></a>

When you restore a DB cluster, the default security group is associated with the restored instance\. As soon as the restore is complete and your new DB cluster is available, you must associate any custom security groups used by the instance you restored from\. You must apply these changes by using the RDS console's *Modify* command, the `ModifyDBInstance` Amazon RDS API, or the AWS CLI `modify-db-instance` command\. 

## Amazon Aurora Considerations<a name="USER_RestoreFromSnapshot.Aurora"></a>

With Aurora, you restore a DB cluster snapshot to a DB cluster\.

With Aurora MySQL, you can also restore a DB cluster snapshot to an Aurora Serverless DB cluster\. For more information, see [Restoring an Aurora Serverless DB Cluster](aurora-serverless.restorefromsnapshot.md)\.

With Aurora MySQL, you can restore a DB cluster snapshot from a cluster without parallel query to a cluster with parallel query\. Because parallel query is typically used with very large tables, the snapshot mechanism is the fastest way to ingest large volumes of data to an Aurora MySQL parallel query\-enabled cluster\. For more information, see [Working with Parallel Query for Amazon Aurora MySQL](aurora-mysql-parallel-query.md)\.

## Restoring from a Snapshot<a name="USER_RestoreFromSnapshot.Restoring"></a>

You can restore a DB cluster from a DB cluster snapshot using the AWS Management Console, the AWS CLI, or the RDS API\.

### AWS Management Console<a name="USER_RestoreFromSnapshot.CON"></a>

**To restore a DB cluster from a DB cluster snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Choose the DB cluster snapshot that you want to restore from\. 

1. From the **Actions** drop\-down, choose **Restore Snapshot**\. 

1. On the **Restore DB Instance** page, in the **DB Instance Identifier** field, type the name for your restored DB cluster\. 

1. Choose **Restore DB Instance**\. 

1. If you want to restore the functionality of the DB cluster to that of the DB cluster that the snapshot was created from, you must modify the DB cluster to use the security group\. The next steps assume that your DB cluster is in a VPC\. If your DB cluster is not in a VPC, use the EC2 Management Console to locate the security group you need for the DB cluster\. 

   1. Sign in to the AWS Management Console and open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\. 

   1. In the navigation pane, choose **Security Groups**\. 

   1. Select the security group that you want to use for your DB clusters\. If necessary, add rules to link the security group to a security group for an EC2 instance\. For more information, see [A DB Instance in a VPC Accessed by an EC2 Instance in the Same VPC](USER_VPC.Scenarios.md#USER_VPC.Scenario1)\. 

### CLI<a name="USER_RestoreFromSnapshot.CLI"></a>

To restore a DB cluster from a DB cluster snapshot, use the AWS CLI command [restore\-db\-cluster\-from\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-from-snapshot.html)\. 

In this example, you restore from a previously created DB cluster snapshot named *mydbclustersnapshot*\. You restore to a new DB cluster named *mynewdbcluster*\. 

**Example**  
For Linux, OS X, or Unix:  

```
1. aws rds restore-db-cluster-from-snapshot \
2.     --db-cluster-identifier mynewdbcluster \
3.     --snapshot-identifier mydbclustersnapshot
```
For Windows:  

```
1. aws rds restore-db-cluster-from-snapshot ^
2.     --db-instance-identifier mynewdbcluster ^
3.     --db-snapshot-identifier mydbclustersnapshot
```

After the DB cluster has been restored, you must add the DB cluster to the security group used by the DB cluster used to create the DB cluster snapshot if you want the same functionality as that of the previous DB cluster\.

### API<a name="USER_RestoreFromSnapshot.API"></a>

To restore a DB cluster from a DB cluster snapshot, call the Amazon RDS API function [RestoreDBClusterFromSnapshot](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RestoreDBClusterFromSnapshot.html) with the following parameters: 
+ `DBClusterIdentifier` 
+ `SnapshotIdentifier` 