# Deleting an Aurora DB cluster or a DB instance<a name="USER_DeleteInstance"></a>

You can delete individual DB instances in an Aurora DB cluster, the cluster itself, or both\. To delete a DB instance, you specify the name of the instance\. Depending on the method you use to delete a cluster, you either specify the name of the cluster or Aurora deletes the cluster automatically as part of deleting the final DB instance\.
+ If the cluster has more than one instance, deleting one of the instances just reduces the computation capacity of the cluster\. If you delete the primary instance from a cluster that has more than one instance, Aurora promotes one of the reader instances to take its place\. Thus, usually you delete any reader instances from the cluster first\.
+ When you use the AWS Management Console, deleting the final DB instance in a DB cluster also deletes the DB cluster and the data in the cluster volume\.
+ When you use the AWS CLI or the RDS API, you can delete all the DB instances in the cluster without deleting the cluster itself\. Your data remains in the cluster volume\. Later, you can create one or more new DB instances to start using the cluster again\.

**Note**  
When the status for a DB instance is `deleting`, its CA certificate value doesn't appear in the RDS console or in output for AWS CLI commands or RDS API operations\. For more information about CA certificates, see [Using SSL/TLS to encrypt a connection to a DB cluster](UsingWithRDS.SSL.md)\.

## Deletion protection<a name="USER_DeleteInstance.DeletionProtection"></a>

You can only delete clusters that don't have deletion protection enabled\. You can delete DB instances within the cluster, but not the cluster itself\. That way, the cluster volume containing all your data is safe\. Aurora enforces deletion protection for a DB cluster whether you try to delete the cluster using the console, the AWS CLI, or the RDS API\. 

 Deletion protection is enabled by default when you create a production DB cluster using the AWS Management Console\. However, deletion protection is disabled by default if you create a cluster using the AWS CLI or API\. Enabling or disabling deletion protection doesn't cause an outage\. To be able to delete the cluster, modify the cluster and disable deletion protection\. For more information about turning deletion protection on and off, see [Modifying the DB cluster by using the console, CLI, and API](Aurora.Modifying.md#Aurora.Modifying.Cluster)\.

**Tip**  
 Even if all the DB instances are deleted, you can access the data by creating a new DB instance in the cluster\. For information about Aurora clusters that have zero DB instances, see  [Deleting an empty Aurora cluster](#USER_DeleteInstance.Empty)\. 

## Aurora clusters with a single DB instance<a name="USER_DeleteInstance.LastInstance"></a>

If you try to delete the last DB instance in your Aurora cluster, the behavior depends on the method you use\. You can delete the last DB instance using the AWS Management Console\. Doing so also deletes the DB cluster\. You can also delete the last DB instance through the AWS CLI or API, even if the DB cluster has deletion protection enabled\. In this case, the DB cluster itself still exists and your data is preserved\. You can access the data again by attaching a new DB instance to the cluster\.

## Deleting an empty Aurora cluster<a name="USER_DeleteInstance.Empty"></a>

If you use the AWS Management Console, Aurora automatically deletes your cluster when you delete the last DB instance in that cluster\. Thus, the procedures for deleting an empty cluster only apply when you use the AWS CLI or the RDS API\.

To delete an empty Aurora DB cluster by using the AWS CLI, call the [delete\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/delete-db-cluster.html) command\.

To delete an empty Aurora DB cluster by using the Amazon RDS API, call the [DeleteDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteDBInstance.html) operation\.

## Aurora MySQL clusters that are read replicas<a name="USER_DeleteInstance.AuroraReplica"></a>

For Aurora MySQL, you can't delete a DB instance in a DB cluster if both of the following conditions are true:
+ The DB cluster is a read replica of another Aurora DB cluster\.
+ The DB instance is the only instance in the DB cluster\.

To delete a DB instance in this case, first promote the DB cluster so that it's no longer a read replica\. After the promotion completes, you can delete the final DB instance in the DB cluster\. For more information, see [Replicating Amazon Aurora MySQL DB clusters across AWS Regions](AuroraMySQL.Replication.CrossRegion.md)\.

## Deleting a DB instance<a name="USER_DeleteInstance.Deleting"></a>

You can delete a DB instance using the AWS Management Console, the AWS CLI, or the RDS API\.

 For Aurora DB clusters, deleting a DB instance doesn't necessarily delete the entire cluster\. You can delete a DB instance in an Aurora cluster to reduce compute capacity and associated charges when the cluster isn't busy\. For information about the special circumstances for Aurora clusters that have one DB instance or zero DB instances, see [Aurora clusters with a single DB instance](#USER_DeleteInstance.LastInstance) and [Deleting an empty Aurora cluster](#USER_DeleteInstance.Empty)\. 

**Note**  
You can't delete a DB cluster when deletion protection is enabled for it\. For more information, see [Deletion protection](#USER_DeleteInstance.DeletionProtection)\.   
You can disable deletion protection by modifying the DB cluster\. For more information, see [Modifying an Amazon Aurora DB cluster](Aurora.Modifying.md)\.

### Console<a name="USER_DeleteInstance.CON"></a>

**To delete a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the DB instance that you want to delete\.

1. For **Actions**, choose **Delete**\.

1. Enter **delete me** in the box\.

1. Choose **Delete**\.

### AWS CLI<a name="USER_DeleteInstance.CLI"></a>

To delete a DB instance by using the AWS CLI, call the [delete\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/delete-db-instance.html) command and specify the `--db-instance-identifier` value\.

**Example**  
For Linux, macOS, or Unix:  

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

To delete a DB instance by using the Amazon RDS API, call the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteDBInstance.html) operation and specify the `DBInstanceIdentifier` parameter\.