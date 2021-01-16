# Disaster recovery for Amazon Aurora global databases<a name="aurora-global-database-disaster-recovery"></a>

In some cases, your Aurora global database might include more than one secondary AWS Region\. If so, you can choose which AWS Region to fail over to if an outage affects the primary AWS Region\. To help determine which secondary AWS Region to choose, you can monitor the replication lag for all your secondary Regions\. In some cases, one secondary Region might have substantially less replication lag than the others\. If so, that's a good indication that the associated Aurora cluster has enough database and network capacity to take over the full read/write workload for your application\. 

When your global database has multiple secondary Regions, make sure that you detach all the secondary Regions if the primary AWS Region experiences an outage\. Then, you promote one of those secondary Regions to be the new primary AWS Region\. Finally, you create new clusters in each of the other secondary Regions and attach those clusters to your global database\. 

When you promote a secondary cluster to be the primary cluster, you also need to update the endpoints that your applications use to connect to the global database\. To get a new writer endpoint from a newly promoted cluster, you can convert a former reader endpoint by removing `-ro` from the endpoint string\. For example, if a former reader endpoint is `global-16rr-test-cluster-1.cluster-ro-12345678901.us-west-2.rds.amazonaws.com`, then the new promoted writer endpoint is `global-16rr-test-cluster-1.cluster-cps2igpwyrwa.us-west-2.rds.amazonaws.com`\. 

**Topics**
+ [Failover for Amazon Aurora global databases](#aurora-global-database-failover)
+ [Managing recovery for Aurora global databases](#aurora-global-database-manage-recovery)

## Failover for Amazon Aurora global databases<a name="aurora-global-database-failover"></a>

Aurora global databases offer a higher level of failover capability than a default Aurora cluster\. If an entire cluster in one AWS Region becomes unavailable, you can promote another cluster in the global database to have read/write capability\. 

You can manually activate the failover mechanism if a cluster in a different AWS Region is a better choice to be the primary cluster\. For example, you might increase the capacity of one of the secondary clusters and then promote it to be the primary cluster\. Or the balance of activity among the AWS Regions might change, so that switching the primary cluster to a different AWS Region might give lower latency for write operations\. 

The following procedure outlines what to do to promote one of the secondary clusters in an Aurora global database\. 

**To promote a secondary cluster**

Before you begin the procedure, the primary cluster fails or otherwise becomes unavailable\. 

1. Remove the secondary cluster from the Aurora global database\. Doing so promotes the cluster to full read/write capability\. To learn how to remove an Aurora cluster from a global database, see [Removing a cluster from an Amazon Aurora global database](aurora-global-database-managing.md#aurora-global-database-detaching)\. 

1. Reconfigure your application to divert write traffic to the newly promoted cluster\. 
**Important**  
 At this point, stop issuing any DML statements or other write operations to the cluster that became unavailable\. To avoid data inconsistencies between the clusters, known as a *"split\-brain" scenario*, avoid writing to the former primary cluster, even if it comes back online\. 

1.  Create a new Aurora global database with the newly promoted cluster as the primary cluster\. To learn how to create an Aurora global database, see [Creating an Amazon Aurora global database](aurora-global-database-getting-started.md#aurora-global-database-creating)\. 

1. Add to the Aurora global database the AWS Region of the cluster that encountered the issue\. Now that AWS Region contains a new secondary cluster\. To learn how to add an AWS Region to an Aurora global database, see [Adding an AWS Region to an Amazon Aurora global database](aurora-global-database-getting-started.md#aurora-global-database-attaching)\. 

1.  When the outage is resolved and you're ready to assign your original AWS Region as the primary cluster again, perform the same steps in reverse: 

   1. Remove one of the secondary clusters from the Aurora global database\. 

   1. Make that cluster the primary cluster of a new Aurora global database in the original AWS Region\. 

   1. Redirect all the write traffic to the primary cluster in the original AWS Region\. 

   1.  Add an AWS Region to set up one or more secondary clusters in the same AWS Regions as before\. 

## Managing recovery for Aurora global databases<a name="aurora-global-database-manage-recovery"></a>

You can control the point at which a global database resumes, in case of disasters such as network or hardware failures that affect the primary DB cluster\. You manage recovery by setting a recovery point objective \(RPO\) for the global database\. 

**Note**  
Managing the RPO is currently supported for Amazon Aurora with PostgreSQL compatibility\. For a list of supported Aurora PostgreSQL versions and AWS Regions, see [Limitations of Amazon Aurora global databases](aurora-global-database.md#aurora-global-database.limitations)\.

The *recovery point objective* is the oldest age of data that you want recoverable from a secondary DB cluster\. The RPO is used when your database resumes operations in a new AWS Region after a failover\. It represents the amount of data loss measured in time that you're willing to accept on a database failover\.

By using a managed RPO, you can control the maximum amount of data loss as the secondary DB clusters lag behind the primary DB cluster\. This data loss is measured in time and is called the *RPO lag time\.* When you set the RPO, Aurora PostgreSQL enforces it on your global database as follows:

1. Aurora PostgreSQL allows transactions to commit on the primary DB cluster if the RPO lag time of at least one secondary DB cluster is less than the RPO time\. 

1. Aurora PostgreSQL blocks transaction commits if there is no secondary DB cluster that has an RPO lag time less than the RPO time\. If Aurora PostgreSQL starts blocking commits, it inserts an event into the PostgreSQL log file\. It then emits wait events that show the sessions that are blocked\. 

1. Aurora PostgreSQL allows transactions to commit again on the primary DB cluster when the RPO lag time of at least one secondary DB cluster becomes less than the RPO time\. 

This approach ensures that Aurora PostgreSQL doesn't allow transaction commits to complete that would result in a violation of your chosen RPO time\. 

**Topics**
+ [Viewing the recovery point objective](#aurora-global-database-view-rpo)
+ [Setting the recovery point objective](#aurora-global-database-set-rpo)
+ [Disabling the recovery point objective](#aurora-global-database-disable-rpo)

### Viewing the recovery point objective<a name="aurora-global-database-view-rpo"></a>

The recovery point objective \(RPO\) of a global database is stored in the `rds.global_db_rpo` parameter\. To view the current RPO setting of the `rds.global_db_rpo` parameter and other parameters of the cluster parameter group, see [Viewing parameter values for a DB cluster parameter group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.ViewingCluster)\.

### Setting the recovery point objective<a name="aurora-global-database-set-rpo"></a>

You can set the global database's RPO using the AWS Management Console, the AWS CLI, or the RDS API\. For more information, see [Modifying parameters in a DB cluster parameter group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.ModifyingCluster)\.

#### Console<a name="aurora-global-database-set-rpo.Console"></a>

**To set the RPO**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Identify the primary DB cluster's parameter group of the global database\. For more information about a cluster's configuration settings, see [Modifying an Amazon Aurora global database](aurora-global-database-managing.md#aurora-global-database-modifying)\.

1. Open the primary DB cluster parameter group and set the **rds\.global\_db\_rpo** parameter\. For more information, see [Modifying parameters in a DB cluster parameter group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.ModifyingCluster)\.

1. Set the **rds\.global\_db\_rpo** parameter value to the number of seconds you want for the recover point objective\. Valid values are from 20 seconds up to the maximum integer value of 2,147,483,647 \(68 years\)\.

#### AWS CLI<a name="aurora-global-database-set-rpo.CLI"></a>

To set the `rds.global_db_rpo` parameter, use the AWS CLI, use the [modify\-db\-cluster\-parameter\-group](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster-parameter-group.html) command\. 

The following example sets the RPO to 5,000 seconds for the primary DB cluster parameter group named `global_db_cluster_parameter_group`\. Valid values are from 20 seconds up to the maximum integer value of 2,147,483,647 \(68 years\)\.

For Linux, macOS, or Unix:

```
aws rds modify-db-cluster-parameter-group \
    --db-cluster-parameter-group-name global_db_cluster_parameter_group \
    --parameters "ParameterName=rds.global_db_rpo,ParameterValue=5000,ApplyMethod=immediate"
```

For Windows:

```
aws rds modify-db-cluster-parameter-group ^
    --db-cluster-parameter-group-name global_db_cluster_parameter_group ^
    --parameters "ParameterName=rds.global_db_rpo,ParameterValue=5000,ApplyMethod=immediate"
```

#### RDS API<a name="aurora-global-database-set-rpo.API"></a>

To modify the `rds.global_db_rpo` parameter, use the Amazon RDS [ ModifyDBClusterParameterGroup](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBClusterParameterGroup.html) API operation\.

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

For more information on how to reset a parameter with the console, see [Modifying parameters in a DB cluster parameter group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.ModifyingCluster)\.

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