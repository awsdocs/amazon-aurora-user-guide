# Deleting a DB Instance in an Aurora DB Cluster<a name="USER_DeleteInstance"></a>

You can delete a DB instance in a DB cluster, including deleting the primary DB instance in a DB cluster or an Amazon Aurora Replica\. To delete a DB instance, you must specify the name of the instance\.

You can enable deletion protection so that users can't delete a DB cluster\. Deletion protection is enabled by default when you create a production DB cluster using the AWS Management Console\. However, deletion protection is disabled by default if you create a cluster using the AWS CLI or API\.

Aurora enforces deletion protection for a DB cluster whether you perform the operation from the console, the CLI, or the API\. If you try to delete a DB cluster that has deletion protection enabled, you can't do so\. To be certain that you can delete the cluster, modify the cluster and disable deletion protection\.

If you try to delete the cluster's last DB instance, the behavior depends on the method you use\. You can't delete the last DB instance through the AWS Management Console, because doing so also deletes the cluster\. You *can* delete the last DB instance through the AWS CLI or API even if the DB cluster has deletion protection enabled\. In this case, the DB cluster itself still exists and your data is preserved\. You can access the data by attaching new DB instances to the cluster\. For more information about turning deletion protection on and off, see [Modifying the DB Cluster by Using the Console, CLI, and API](Aurora.Modifying.md#Aurora.Modifying.Cluster)\.

For Aurora MySQL, you can't delete a DB instance in a DB cluster if both of the following conditions are true:
+ The DB cluster is a Read Replica of another Aurora DB cluster\.
+ The DB instance is the only instance in the DB cluster\.

To delete a DB instance in this case, first promote the DB cluster so it's no longer a Read Replica\. After the promotion completes, you can delete the final DB instance in the DB cluster\. For more information, see [Replicating Amazon Aurora MySQL DB Clusters Across AWS Regions](AuroraMySQL.Replication.CrossRegion.md)\.

## Deleting a DB Instance by Using the Console, CLI, and API<a name="USER_DeleteInstance.Deleting"></a>

You can delete a DB instance using the AWS Management Console, the AWS CLI, or the RDS API\.

### Console<a name="USER_DeleteInstance.CON"></a>

**To delete a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the DB instance that you want to delete\. 

1. For **Actions**, choose **Delete**\. 

1. To retain automated backups, choose **Retain automated backups**\.

1. Enter **delete me** in the box\.

1. Choose **Delete**\. 

### AWS CLI<a name="USER_DeleteInstance.CLI"></a>

To delete a DB instance by using the AWS CLI, call the [delete\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/delete-db-instance.html) command and specify the `--db-instance-identifier` option\. 

**Example**  
For Linux, OS X, or Unix:  

```
aws rds delete-db-instance \
    --db-instance-identifier mydbinstance
```
For Windows:  

```
aws rds delete-db-instance ^
    --db-instance-identifier mydbinstance
```

### RDS API<a name="USER_DeleteInstance.API"></a>

To delete a DB instance by using the Amazon RDS API, call the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteDBInstance.html) action and specify the `DBInstanceIdentifier` parameter\. 