# Using failover in an Amazon Aurora global database<a name="aurora-global-database-disaster-recovery"></a>

An Aurora global database provides more comprehensive failover capabilities than the [failover provided by a default Aurora DB cluster](Concepts.AuroraHighAvailability.md)\. By using an Aurora global database, you can plan for and recover from disaster fairly quickly\. Recovery from disaster is typically measured using values for RTO and RPO\.
+ **Recovery time objective \(RTO\)** – The time it takes a system to return to a working state after a disaster\. In other words, RTO measures downtime\. For an Aurora global database, RTO can be in the order of minutes\. 
+ **Recovery point objective \(RPO\)** – The amount of data that can be lost \(measured in time\)\. For an Aurora global database, RPO is typically measured in seconds\. With an Aurora PostgreSQL–based global database, you can use the `rds.global_db_rpo` parameter to set and track the upper bound on RPO, but doing so might affect transaction processing on the primary cluster's writer node\. For more information, see [Managing RPOs for Aurora PostgreSQL–based global databases](#aurora-global-database-manage-recovery)\. 

With an Aurora global database, there are two different approaches to failover depending on the scenario\. 
+ **Manual unplanned failover** \("detach and promote"\) – To recover from an unplanned outage or to do disaster recovery \(DR\) testing, perform a cross\-Region failover to one of the secondaries in your Aurora global database\. The RTO for this manual process depends on how quickly you can perform the tasks listed in [Recovering an Amazon Aurora global database from an unplanned outage](#aurora-global-database-failover)\. The RPO is typically measured in seconds, but this depends on the Aurora storage replication lag across the network at the time of the failure\.
+ **Managed planned failover** – This feature is intended for controlled environments, such as operational maintenance and other planned operational procedures\. By using managed planned failover, you can relocate the primary DB cluster of your Aurora global database to one of the secondary Regions\. Because this feature synchronizes secondary DB clusters with the primary before making any other changes, RPO is 0 \(no data loss\)\. To learn more, see [Performing managed planned failovers for Amazon Aurora global databases](#aurora-global-database-disaster-recovery.managed-failover)\. 

**Note**  
Aurora global databases can't fail over from a primary to a secondary DB cluster when the primary and secondary have different DB engine versions—whether major, minor, or patch versions\.

**Topics**
+ [Recovering an Amazon Aurora global database from an unplanned outage](#aurora-global-database-failover)
+ [Performing managed planned failovers for Amazon Aurora global databases](#aurora-global-database-disaster-recovery.managed-failover)
+ [Managing RPOs for Aurora PostgreSQL–based global databases](#aurora-global-database-manage-recovery)

## Recovering an Amazon Aurora global database from an unplanned outage<a name="aurora-global-database-failover"></a>

On very rare occasions, your Aurora global database might experience an unexpected outage in its primary AWS Region\. If this happens, your primary Aurora DB cluster and its writer node aren't available, and the replication between the primary cluster and the secondaries ceases\. To minimize both downtime \(RTO\) and data loss \(RPO\), you can work quickly to perform a cross\-Region failover and reconstruct your Aurora global database\. 

**Tip**  
We recommend that you understand this process before using it\. Have a plan ready to quickly proceed at the first sign of a Region\-wide issue\. Be ready to identify the secondary Region with the least lag time\. Use Amazon CloudWatch regularly to track lag times for the secondary clusters\. Make sure to test your plan to check that your procedures are complete and accurate, and that staff are trained to perform a DR failover before it really happens\.

**To fail over to a secondary cluster after an unplanned outage in the primary Region**

1. Stop issuing DML statements and other write operations to the primary Aurora DB cluster in the AWS Region with the outage\. 

1. Identify an Aurora DB cluster from a secondary AWS Region to use as a new primary DB cluster\. If you have two or more secondary AWS Regions in your Aurora global database, choose the secondary cluster that has the least lag time\.

1. Detach your chosen secondary DB cluster from the Aurora global database\.

   Removing a secondary DB cluster from an Aurora global database immediately stops the replication from the primary to this secondary and promotes it to a standalone provisioned Aurora DB cluster with full read/write capabilities\. Any other secondary Aurora DB clusters associated with the primary cluster in the Region with the outage are still available and can accept calls from your application\. They also consume resources\. Because you're recreating the Aurora global database, remove the other secondary DB clusters before creating the new Aurora global database in the following steps\. Doing this avoids data inconsistencies among the DB clusters in the Aurora global database \(*split\-brain* issues\)\.

   For detailed steps for detaching, see [Removing a cluster from an Amazon Aurora global database](aurora-global-database-managing.md#aurora-global-database-detaching)\. 

1. Reconfigure your application to send all write operations to this now standalone Aurora DB cluster using its new endpoint\. If you accepted the provided names when you created the Aurora global database, you can change the endpoint by removing the `-ro` from the cluster's endpoint string in your application\.

   For example, the secondary cluster's endpoint `my-global.cluster-ro-aaaaaabbbbbb.us-west-1.rds.amazonaws.com` becomes `my-global.cluster-aaaaaabbbbbb.us-west-1.rds.amazonaws.com` when that cluster is detached from the Aurora global database\. 

   This Aurora DB cluster becomes the primary cluster of a new Aurora global database when you start adding Regions to it in the next step\. 

   If you are using RDS Proxy, make sure to redirect your application's write operations to the appropriate read/write endpoint of the proxy that's associated with the new primary cluster\. This proxy endpoint might be the default endpoint or a custom read/write endpoint\. For more information see [How RDS Proxy endpoints work with global databases](rds-proxy-gdb.md#rds-proxy-gdb.endpoints)\.

1. Add an AWS Region to the DB cluster\. When you do this, the replication process from primary to secondary begins\. For detailed steps to add a Region, see [Adding an AWS Region to an Amazon Aurora global database](aurora-global-database-getting-started.md#aurora-global-database-attaching)\. 

1. Add more AWS Regions as needed to recreate the topology needed to support your application\. 

Make sure that application writes are sent to the correct Aurora DB cluster before, during, and after making these changes\. Doing this avoids data inconsistencies among the DB clusters in the Aurora global database \(*split\-brain* issues\)\.

If you reconfigured in response to an outage in an AWS Region, you can make that AWS Region the primary again after the outage is resolved\. To do so, you add the old AWS Region to your new global database, and then use the managed planned failover process to switch its role\. Your Aurora global database must use a version of Aurora PostgreSQL or Aurora MySQL that supports managed planned failovers\. For more information, see [Performing managed planned failovers for Amazon Aurora global databases](#aurora-global-database-disaster-recovery.managed-failover)\. 

## Performing managed planned failovers for Amazon Aurora global databases<a name="aurora-global-database-disaster-recovery.managed-failover"></a>



By using managed planned failovers, you can relocate the primary cluster of your Aurora global database to a different AWS Region on a routine basis\.  This approach is intended for controlled environments, such as operational maintenance and other planned operational procedures\. 

As an example, say a financial institution headquartered in New York has branch offices located in San Francisco, the UK, and Europe\. The organization's core business applications use an Aurora global database\. Its primary cluster runs in the US East \(Ohio\) Region\. It has secondary clusters running in the US West \(N\. California\) Region, Europe \(London\) Region, and the Europe \(Frankfurt\) Region\. Every quarter, it relocates the primary cluster from the \(current\) primary AWS Region to the secondary Region designated for that rotation\. 

**Note**  
Managed *planned* failover is designed to be used on a healthy Aurora global database\. To recover from an unplanned outage or to do disaster recovery \(DR\) testing, follow the "detach and promote" process detailed in [Recovering an Amazon Aurora global database from an unplanned outage](#aurora-global-database-failover)\. 

During a managed planned failover, your primary cluster is failed over to your choice of secondary Region while your Aurora global database's existing replication topology is maintained\. Before the managed planned failover process begins, Aurora global database synchronizes all secondary clusters with its primary cluster\. After ensuring that all clusters are synchronized, the managed planned failover begins\. The DB cluster in the primary Region becomes read\-only\. The chosen secondary cluster promotes one of its read\-only nodes to full writer status, thus allowing the cluster to assume the role of primary cluster\. Because all secondary clusters were synchronized with the primary at the beginning of the process, the new primary continues operations for the Aurora global database without losing any data\. Your database is unavailable for a short time while the primary and selected secondary clusters are assuming their new roles\. 

To optimize application availability, we recommend that you do the following before using this feature: 
+ Perform this operation during nonpeak hours or at another time when writes to the primary DB cluster are minimal\. 
+ Take applications offline to prevent writes from being sent to the primary cluster of Aurora global database\. 
+ Check lag times for all secondary Aurora DB clusters in the Aurora global database\. Choose the secondary with the least overall lag time for the managed planned failover\. Use Amazon CloudWatch to view the `AuroraGlobalDBReplicationLag` metric for all secondaries\. This metric tells you how far behind \(in milliseconds\) a secondary is to the primary DB cluster\. Its value is directly proportional to the time it'll take for Aurora to complete failover\. In other words, the larger the lag value, the longer the outage, so choose the Region with the least lag\.

  For more information about CloudWatch metrics for Aurora, see [Cluster\-level metrics for Amazon Aurora](Aurora.AuroraMySQL.Monitoring.Metrics.md#Aurora.AuroraMySQL.Monitoring.Metrics.clusters)\. 

During a managed planned failover, the chosen secondary DB cluster is promoted to its new role as primary\. However, it doesn't inherit the various configuration options of the primary DB cluster\. A mismatch in configuration can lead to performance issues, workload incompatibilities, and other anomalous behavior\. To avoid such issues, we recommend that you resolve differences between your Aurora global database clusters for the following: 
+ **Configure Aurora DB cluster parameter group for the new primary, if necessary** – You can configure your Aurora DB cluster parameter groups independently for each Aurora cluster in your Aurora global database\. That means that when you promote a secondary DB cluster to take over the primary role, the parameter group from the secondary might be configured differently than for the primary\. If so, modify the promoted secondary DB cluster's parameter group to conform to your primary cluster's settings\. To learn how, see [Modifying parameters for an Aurora global database](aurora-global-database-managing.md#aurora-global-database-modifying.parameters)\. 
+ **Configure monitoring tools and options, such as Amazon CloudWatch Events and alarms** – Configure the promoted DB cluster with the same logging ability, alarms, and so on as needed for the global database\. As with parameter groups, configuration for these features isn't inherited from the primary during the failover process\. For more information about Aurora DB clusters and monitoring, see [Overview of monitoring Amazon Aurora](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/MonitoringOverview.html#monitoring-cloudwatch)\.
+ **Configure integrations with other AWS services** – If your Aurora global database integrates with AWS services, such as AWS Secrets Manager, AWS Identity and Access Management, Amazon S3, and AWS Lambda, you need to make sure these are configured as needed\. For more information about integrating Aurora global databases with IAM, Amazon S3 and Lambda, see [Using Amazon Aurora global databases with other AWS services](aurora-global-database-interop.md)\. To learn more about Secrets Manager, see [How to automate replication of secrets in AWS Secrets Manager across AWS Regions](http://aws.amazon.com/blogs/security/how-to-automate-replication-of-secrets-in-aws-secrets-manager-across-aws-regions/)\. 

When the failover process completes, the promoted Aurora DB cluster can handle write operations for the Aurora global database\. Make sure to change the endpoint for your application to use the new endpoint\. If you accepted the provided names when you created the Aurora global database, you can change the endpoint by removing the `-ro` from the promoted cluster's endpoint string in your application\.

For example, the secondary cluster's endpoint `my-global.cluster-ro-aaaaaabbbbbb.us-west-1.rds.amazonaws.com` becomes `my-global.cluster-aaaaaabbbbbb.us-west-1.rds.amazonaws.com` when that cluster is promoted to primary\. 

If you are using RDS Proxy, make sure to redirect your application's write operations to the appropriate read/write endpoint of the proxy that's associated with the new primary cluster\. This proxy endpoint might be the default endpoint or a custom read/write endpoint\. For more information see [How RDS Proxy endpoints work with global databases](rds-proxy-gdb.md#rds-proxy-gdb.endpoints)\.

You can fail over your Aurora global database using the AWS Management Console, the AWS CLI, or the RDS API\.

### Console<a name="aurora-global-database-disaster-recovery.managed-failover.console"></a>

**To start the failover process on your Aurora global database**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose **Databases** and find the Aurora global database you want to fail over\.

1. Choose **Fail over global database** in the **Actions** menu\. The failover process doesn't begin until after you choose the failover target in the next step\. At this point, the failover is pending\.   
![\[Starting the failover process for an Aurora global database\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-db-managed-failover-1.png)

   1. Choose the secondary Aurora DB cluster that you want to promote to primary\. The secondary DB cluster must be **available**\. If you have more than one secondary DB cluster, you can compare the **lag** amount for all secondaries and choose the one with the smallest amount of lag\.  
![\[Choosing the secondary DB cluster to promote\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-db-managed-failover-2.png)

   1. Choose **Fail over global database** to confirm your choice of secondary DB cluster and begin the failover process\.
**Tip**  
The failover process can take some time to complete\. You can cancel once the process is underway, but it can take some time to return your Aurora global database to its original configuration\.   
![\[Secondary cluster selector and confirmation card for the failover process\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-db-managed-failover-3.png)

      The **Status** column of the Databases list shows the state of each Aurora DB instance and Aurora DB cluster during the failover process\.  
![\[Failover in action. Status column shows state of process.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-db-managed-failover-4.png)

      The status bar at the top of the Console displays progress and provides a **Cancel failover** option\. If you choose **Cancel failover**, you're given the option to proceed with the failover or to cancel the failover process\.   
![\[Cancel failover alert\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-db-managed-failover-cancel.png)

      Choose **Cancel failover** if you want to cancel the failover\.

   1. Choose **Close** to continue failing over and dismiss the prompt\. 

When the failover completes, you can see the Aurora DB clusters and their current state in the **Databases** list, as shown following\.

![\[Failover in action. Status column shows state of process.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-db-managed-failover-5.png)

### AWS CLI<a name="aurora-global-database-disaster-recovery.managed-failover.cli"></a>

**To fail over an Aurora global database**

Use the `[failover\-global\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/failover-global-cluster.html)` CLI command to fail over your Aurora global database\. With the command, pass values for the following parameters\. 
+ `--region` – Specify the AWS Region where the primary DB cluster of the Aurora global database is running\.
+ `--global-cluster-identifier` – Specify the name of your Aurora global database\. 
+ `--target-db-cluster-identifier` – Specify the Amazon Resource Name \(ARN\) of the Aurora DB cluster that you want to promote to be the primary for the Aurora global database\. 

For Linux, macOS, or Unix:

```
aws rds --region aws-Region \
   failover-global-cluster --global-cluster-identifier global_database_id \
  --target-db-cluster-identifier ARN-of-secondary-to-promote
```

For Windows:

```
aws rds --region aws-Region ^
   failover-global-cluster --global-cluster-identifier global_database_id ^
  --target-db-cluster-identifier ARN-of-secondary-to-promote
```

### RDS API<a name="aurora-global-database-disaster-recovery.managed-failover.api"></a>

To fail over an Aurora global database, run the [FailoverGlobalCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_FailoverGlobalCluster.html) API operation\.

## Managing RPOs for Aurora PostgreSQL–based global databases<a name="aurora-global-database-manage-recovery"></a>

With an Aurora PostgreSQL–based global database, you can manage the recovery point objective \(RPO\) for your Aurora global database by using PostgreSQL's `rds.global_db_rpo` parameter\. RPO represents maximum amount of data that can be lost in the event of an outage\. 

When you set an RPO for your Aurora PostgreSQL–based global database, Aurora monitors the *RPO lag time* of all secondary clusters to make sure that at least one secondary cluster stays within the target RPO window\. RPO lag time is another time\-based metric\. 

The RPO is used when your database resumes operations in a new AWS Region after a failover\. Aurora evaluates RPO and RPO lag times to commit \(or block\) transactions on the primary as follows: 
+ Commits the transaction if at least one secondary DB cluster has an RPO lag time less than the RPO\.
+ Blocks the transaction if all secondary DB clusters have RPO lag times that are larger than the RPO\. It also logs the event to the PostgreSQL log file and emits "wait" events that show the blocked sessions\.

In other words, if all secondary clusters are behind the target RPO, Aurora pauses transactions on the primary cluster until at least one of the secondary clusters catches up\. Paused transactions are resumed and committed as soon as the lag time of at least one secondary DB cluster becomes less than the RPO\. The result is that no transactions can commit until the RPO is met\.

If you set this parameter as outlined in the following, you can then also monitor the metrics that it generates\. You can do so by using `psql` or another tool to query the Aurora global database's primary DB cluster and obtain detailed information about your Aurora PostgreSQL–based global database's operations\. To learn how, see [Monitoring Aurora PostgreSQL\-based Aurora global databases](aurora-global-database-monitoring.md#aurora-global-database-monitoring.postgres)\.

**Topics**
+ [Setting the recovery point objective](#aurora-global-database-set-rpo)
+ [Viewing the recovery point objective](#aurora-global-database-view-rpo)
+ [Disabling the recovery point objective](#aurora-global-database-disable-rpo)

### Setting the recovery point objective<a name="aurora-global-database-set-rpo"></a>

The `rds.global_db_rpo` parameter controls the RPO setting for a PostgreSQL database\. This parameter is supported by Aurora PostgreSQL\. Valid values for `rds.global_db_rpo` range from 20 seconds to 2,147,483,647 seconds \(68 years\)\. Choose a realistic value to meet your business need and use case\. For example, you might want to allow up to 10 minutes for your RPO, in which case you set the value to 600\.

 You can set this value for your Aurora PostgreSQL–based global database by using the AWS Management Console, the AWS CLI, or the RDS API\.

#### Console<a name="aurora-global-database-set-rpo.Console"></a>

**To set the RPO**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose the primary cluster of your Aurora global database and open the **Configuration** tab to find its DB cluster parameter group\. For example, the default parameter group for a primary DB cluster running Aurora PostgreSQL 11\.7 is `default.aurora-postgresql11`\.

   Parameter groups can't be edited directly\. Instead, you do the following:
   + Create a custom DB cluster parameter group using the appropriate default parameter group as the starting point\. For example, create a custom DB cluster parameter group based on the `default.aurora-postgresql11`\.
   + On your custom DB parameter group, set the value of the **rds\.global\_db\_rpo** parameter to meet your use case\. Valid values range from 20 seconds up to the maximum integer value of 2,147,483,647 \(68 years\)\.
   + Apply the modified DB cluster parameter group to your Aurora DB cluster\. 

For more information, see [Modifying parameters in a DB cluster parameter group](USER_WorkingWithDBClusterParamGroups.md#USER_WorkingWithParamGroups.ModifyingCluster)\.

#### AWS CLI<a name="aurora-global-database-set-rpo.CLI"></a>

To set the `rds.global_db_rpo` parameter, use the [modify\-db\-cluster\-parameter\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster-parameter-group.html) CLI command\. In the command, specify the name of your primary cluster's parameter group and values for RPO parameter\.

The following example sets the RPO to 600 seconds \(10 minutes\) for the primary DB cluster's parameter group named `my_custom_global_parameter_group`\. 

For Linux, macOS, or Unix:

```
aws rds modify-db-cluster-parameter-group \
    --db-cluster-parameter-group-name my_custom_global_parameter_group \
    --parameters "ParameterName=rds.global_db_rpo,ParameterValue=600,ApplyMethod=immediate"
```

For Windows:

```
aws rds modify-db-cluster-parameter-group ^
    --db-cluster-parameter-group-name my_custom_global_parameter_group ^
    --parameters "ParameterName=rds.global_db_rpo,ParameterValue=600,ApplyMethod=immediate"
```

#### RDS API<a name="aurora-global-database-set-rpo.API"></a>

To modify the `rds.global_db_rpo` parameter, use the Amazon RDS [ ModifyDBClusterParameterGroup](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBClusterParameterGroup.html) API operation\.

### Viewing the recovery point objective<a name="aurora-global-database-view-rpo"></a>

The recovery point objective \(RPO\) of a global database is stored in the `rds.global_db_rpo` parameter for each DB cluster\. You can connect to the endpoint for the secondary cluster you want to view and use `psql` to query the instance for this value\.

```
db-name=>show rds.global_db_rpo;
```

If this parameter isn't set, the query returns the following:

```
rds.global_db_rpo
-------------------
 -1
(1 row)
```

This next response is from a secondary DB cluster that has 1 minute RPO setting\.

```
rds.global_db_rpo
-------------------
 60
(1 row)
```

You can also use the CLI to get values for find out if `rds.global_db_rpo` is active on any of the Aurora DB clusters by using the CLI to get values of all `user` parameters for the cluster\. 

For Linux, macOS, or Unix:

```
aws rds describe-db-cluster-parameters \
 --db-cluster-parameter-group-name lab-test-apg-global \
 --source user
```

For Windows:

```
aws rds describe-db-cluster-parameters ^
 --db-cluster-parameter-group-name lab-test-apg-global *
 --source user
```

The command returns output similar to the following for all `user` parameters\. that aren't `default-engine` or `system` DB cluster parameters\. 

```
{
    "Parameters": [
        {
            "ParameterName": "rds.global_db_rpo",
            "ParameterValue": "60",
            "Description": "(s) Recovery point objective threshold, in seconds, that blocks user commits when it is violated.",
            "Source": "user",
            "ApplyType": "dynamic",
            "DataType": "integer",
            "AllowedValues": "20-2147483647",
            "IsModifiable": true,
            "ApplyMethod": "immediate",
            "SupportedEngineModes": [
                "provisioned"
            ]
        }
    ]
}
```

To learn more about viewing parameters of the cluster parameter group, see [Viewing parameter values for a DB cluster parameter group](USER_WorkingWithDBClusterParamGroups.md#USER_WorkingWithParamGroups.ViewingCluster)\.

### Disabling the recovery point objective<a name="aurora-global-database-disable-rpo"></a>

To disable the RPO, reset the `rds.global_db_rpo` parameter\. You can reset parameters using the AWS Management Console, the AWS CLI, or the RDS API\.

#### Console<a name="aurora-global-database-set-rpo.Console"></a>

**To disable the RPO**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

1. In the list, choose your primary DB cluster parameter group\.

1. Choose **Edit parameters**\.

1. Choose the box next to the **rds\.global\_db\_rpo** parameter\.

1. Choose **Reset**\. 

1. When the screen shows **Reset parameters in DB parameter group**, choose **Reset parameters**\.

For more information on how to reset a parameter with the console, see [Modifying parameters in a DB cluster parameter group](USER_WorkingWithDBClusterParamGroups.md#USER_WorkingWithParamGroups.ModifyingCluster)\.

#### AWS CLI<a name="aurora-global-database-set-rpo.CLI"></a>

To reset the `rds.global_db_rpo` parameter, use the [reset\-db\-cluster\-parameter\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/reset-db-cluster-parameter-group.html) command\.

For Linux, macOS, or Unix:

```
aws rds reset-db-cluster-parameter-group \
    --db-cluster-parameter-group-name global_db_cluster_parameter_group \
    --parameters "ParameterName=rds.global_db_rpo,ApplyMethod=immediate"
```

For Windows:

```
aws rds reset-db-cluster-parameter-group ^
    --db-cluster-parameter-group-name global_db_cluster_parameter_group ^
    --parameters "ParameterName=rds.global_db_rpo,ApplyMethod=immediate"
```

#### RDS API<a name="aurora-global-database-set-rpo.API"></a>

To reset the `rds.global_db_rpo` parameter, use the Amazon RDS API [ ResetDBClusterParameterGroup](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ResetDBClusterParameterGroup.html) operation\.