# Rebooting a DB Instance in a DB Cluster<a name="USER_RebootInstance"></a>

You might need to reboot your DB instance, usually for maintenance reasons\. For example, if you make certain modifications, or if you change the DB parameter group associated with the DB instance  or its DB cluster, you must reboot the instance for the changes to take effect\. 

**Note**  
If a DB instance isn't using the latest changes to its associated DB parameter group, the AWS Management Console shows the DB parameter group with a status of **pending\-reboot**\. The **pending\-reboot** parameter groups status doesn't result in an automatic reboot during the next maintenance window\. To apply the latest parameter changes to that DB instance, manually reboot the DB instance\. For more information about parameter groups, see [Working with DB Parameter Groups and DB Cluster Parameter Groups](USER_WorkingWithParamGroups.md)\.

Rebooting a DB instance restarts the database engine service\. Rebooting a DB instance results in a momentary outage, during which the DB instance status is set to *rebooting*\. 

You can't reboot your DB instance if it is not in the available state\. Your database can be unavailable for several reasons, such as an in\-progress backup, a previously requested modification, or a maintenance\-window action\. 

**Important**  
When you reboot the primary instance of an Amazon Aurora DB cluster, RDS also automatically restarts all of the Aurora Replicas in that DB cluster\. When you reboot the primary instance of an Aurora DB cluster, no failover occurs\. When you reboot an Aurora Replica, no failover occurs\. To fail over an Aurora DB cluster, call the AWS CLI command [https://docs.aws.amazon.com/cli/latest/reference/rds/failover-db-cluster.html](https://docs.aws.amazon.com/cli/latest/reference/rds/failover-db-cluster.html), or the API operation [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_FailoverDBCluster.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_FailoverDBCluster.html)\. 

## Console<a name="USER_RebootInstance.Console"></a>

**To reboot a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**, and then choose the DB instance that you want to reboot\. 

1. For **Actions**, choose **Reboot**\. 

   The **Reboot DB Instance** page appears\.

1. Choose **Reboot** to reboot your DB instance\. 

   Alternatively, choose **Cancel**\. 

## AWS CLI<a name="USER_RebootInstance.CLI"></a>

To reboot a DB instance by using the AWS CLI, call the [https://docs.aws.amazon.com/cli/latest/reference/rds/reboot-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/reboot-db-instance.html) command\. 

**Example Simple Reboot**  
For Linux, macOS, or Unix:  

```
aws rds reboot-db-instance \
    --db-instance-identifier mydbinstance
```
For Windows:  

```
aws rds reboot-db-instance ^
    --db-instance-identifier mydbinstance
```

## RDS API<a name="USER_RebootInstance.API"></a>

To reboot a DB instance by using the Amazon RDS API, call the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RebootDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RebootDBInstance.html) action\. 