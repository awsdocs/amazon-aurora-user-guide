# Deleting a DB Cluster Snapshot<a name="USER_DeleteSnapshot"></a>

You can delete DB cluster snapshots managed by Amazon RDS when you no longer need them\.

**Note**  
To delete backups managed by AWS Backup, use the AWS Backup console\. For information about AWS Backup, see the [https://docs.aws.amazon.com/aws-backup/latest/devguide](https://docs.aws.amazon.com/aws-backup/latest/devguide)\.

## Deleting a DB Cluster Snapshot<a name="USER_DeleteDBClusterSnapshot"></a>

You can delete a DB cluster snapshot using the console, the AWS CLI, or the RDS API\.

To delete a shared or public snapshot, you must sign in to the AWS account that owns the snapshot\.

### Console<a name="USER_DeleteSnapshot.CON"></a>

**To delete a DB cluster snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Choose the DB cluster snapshot that you want to delete\.

1. For **Actions**, choose **Delete Snapshot**\. 

1. Choose **Delete** on the confirmation page\. 

### AWS CLI<a name="USER_DeleteSnapshot.CLI"></a>

You can delete a DB cluster snapshot by using the AWS CLI command [delete\-db\-cluster\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/delete-db-cluster-snapshot.html)\. 

The following options are used to delete a DB cluster snapshot\. 
+ `--db-cluster-snapshot-identifier` – The identifier for the DB cluster snapshot\. 

**Example**  
The following code deletes the `mydbclustersnapshot` DB cluster snapshot\.   
For Linux, macOS, or Unix:  

```
1. aws rds delete-db-cluster-snapshot \
2.     --db-cluster-snapshot-identifier mydbclustersnapshot
```
For Windows:  

```
1. aws rds delete-db-cluster-snapshot ^
2.     --db-cluster-snapshot-identifier mydbclustersnapshot
```

### RDS API<a name="USER_DeleteSnapshot.API"></a>

You can delete a DB cluster snapshot by using the Amazon RDS API operation [DeleteDBClusterSnapshot](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteDBClusterSnapshot.html)\. 

The following parameters are used to delete a DB cluster snapshot\. 
+ `DBClusterSnapshotIdentifier` – The identifier for the DB cluster snapshot\. 