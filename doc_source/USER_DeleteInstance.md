# Deleting a DB Instance in a DB Cluster<a name="USER_DeleteInstance"></a>

You can delete a DB instance in a DB cluster\. You can delete the primary DB instance in the DB cluster or an Aurora Replica\. To delete a DB instance, you must specify the name of the instance\. 

For Aurora MySQL, you can't delete the DB instance in a DB cluster if both of the following conditions are true:
+ The DB cluster is a Read Replica of another Aurora DB cluster\.
+ The DB instance is the only instance in the DB cluster\.

To delete a DB instance in this case, first promote the DB cluster so it's no longer a Read Replica\. After the promotion completes, you can delete the final DB instance in the DB cluster\. For more information, see [Replicating Amazon Aurora MySQL DB Clusters Across AWS Regions](AuroraMySQL.Replication.CrossRegion.md)\.

## Delete a DB Instance<a name="USER_DeleteInstance.Deleting"></a>

You can delete a DB instance using the AWS Management Console, the AWS CLI, or the RDS API\.

### AWS Management Console<a name="USER_DeleteInstance.CON"></a>

**To delete a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Instances**, and then select the DB instance that you want to delete\. 

1. Choose **Instance actions**, and then choose **Delete**\. 

1. Type **delete me** in the box\.

1. Choose **Delete**\. 

### CLI<a name="USER_DeleteInstance.CLI"></a>

To delete a DB instance by using the AWS CLI, call the [delete\-db\-instance](http://docs.aws.amazon.com/cli/latest/reference/rds/delete-db-instance.html) command and specify the `--db-instance-identifier` option\. 

**Example**  
For Linux, OS X, or Unix:  

```
1. aws rds delete-db-instance \
2.     --db-instance-identifier mydbinstance
```
For Windows:  

```
1. aws rds delete-db-instance ^
2.     --db-instance-identifier mydbinstance
```

### API<a name="USER_DeleteInstance.API"></a>

To delete a DB instance by using the Amazon RDS API, call the [http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteDBInstance.html](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteDBInstance.html) action and specify the `DBInstanceIdentifier` parameter\. 