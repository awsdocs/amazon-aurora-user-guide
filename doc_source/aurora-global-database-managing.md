# Managing an Amazon Aurora global database<a name="aurora-global-database-managing"></a>

With the exception of the managed planned failover process, you perform most management operations on the individual clusters that make up an Aurora global database\. When you choose **Group related resources** on the **Databases** page in the console, you see the primary cluster and secondary clusters grouped under the associated global database\. To find the AWS Regions where a global database's DB clusters are running, its Aurora DB engine and version, and its identifier, use its **Configuration** tab\.

The managed planned failover process is available to Aurora global database objects only, not for a single Aurora DB cluster\. To learn more, see [Managed planned failover for Amazon Aurora global databases](aurora-global-database-disaster-recovery.md#aurora-global-database-disaster-recovery.managed-failover)\. 

To recover an Aurora global database from an unplanned outage in its primary Region, see [Disaster recovery and Amazon Aurora global databases](aurora-global-database-disaster-recovery.md)\. 

**Topics**
+ [Modifying an Amazon Aurora global database](#aurora-global-database-modifying)
+ [Modifying parameters for an Aurora global database](#aurora-global-database-modifying.parameters)
+ [Removing a cluster from an Amazon Aurora global database](#aurora-global-database-detaching)
+ [Deleting an Amazon Aurora global database](#aurora-global-database-deleting)

## Modifying an Amazon Aurora global database<a name="aurora-global-database-modifying"></a>

The **Databases** page in the AWS Management Console lists all your Aurora global databases, showing the primary cluster and secondary clusters for each one\. The Aurora global database has its own configuration settings\. Specifically, it has AWS Regions associated with its primary and secondary clusters, as shown in the screenshot following\.

![\[Screenshot showing a selected Aurora global database and its configuration settings in the AWS Management Console.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-db-global-database-configuration.png)

When you make changes to the Aurora global database, you have a chance to cancel changes, as shown in the following screenshot\.

![\[Screenshot showing the page to modify settings for an Aurora global database.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-databases-modify-global-01.png)

When you choose **Continue**, you confirm the changes\. 

## Modifying parameters for an Aurora global database<a name="aurora-global-database-modifying.parameters"></a>

You can configure the Aurora DB cluster parameter groups independently for each Aurora cluster within the Aurora global database\. Most parameters work the same as for other kinds of Aurora clusters\. We recommend that you keep settings consistent among all the clusters in a global database\. Doing this helps to avoid unexpected behavior changes if you promote a secondary cluster to be the primary\.

For example, use the same settings for time zones and character sets to avoid inconsistent behavior if a different cluster takes over as the primary cluster\. 

The `aurora_enable_repl_bin_log_filtering` and `aurora_enable_replica_log_compression` configuration settings have no effect\. 

## Removing a cluster from an Amazon Aurora global database<a name="aurora-global-database-detaching"></a>

You can remove Aurora DB clusters from your Aurora global database for several different reasons\. For example, you might want to remove an Aurora DB cluster from an Aurora global database if the primary cluster becomes degraded or isolated\. It then becomes a standalone provisioned Aurora DB cluster that could be used to create a new Aurora global database\. To learn more, see [Recovering an Amazon Aurora global database from an unplanned outage](aurora-global-database-disaster-recovery.md#aurora-global-database-failover)\. 

You also might want to remove Aurora DB clusters because you want to delete an Aurora global database that you no longer need\. You can't delete the Aurora global database until after you remove \(detach\) all associated Aurora DB clusters, leaving the primary for last\. For more information, see [Deleting an Amazon Aurora global database](#aurora-global-database-deleting)\.

When an Aurora DB cluster is detached from the Aurora global database, it's no longer synchronized with the primary\. It becomes a standalone provisioned Aurora DB cluster with full read/write capabilities\.

You can remove Aurora DB clusters from your Aurora global database using the AWS Management Console, the AWS CLI, or the RDS API\. 

### Console<a name="aurora-global-database-detach.console"></a>

**To remove an Aurora cluster from an Aurora global database**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose the cluster on the **Databases** page\.

1. For **Actions**, choose **Remove from Global**\.   
![\[Screenshot showing selected Aurora DB cluster (secondary) and "Remove from global" Action.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-db-detach-secondary-01.png)

   You see a prompt to confirm that you want to detach the secondary from the Aurora global database\.   
![\[Screenshot showing confirmation prompt to remove a secondary cluster from an Aurora global database.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-db-detach-secondary-02.png)

1. Choose **Remove and promote** to remove the cluster from the global database\. 

The Aurora DB cluster is no longer serving as a secondary in the Aurora global database, and is no longer synchronized with the primary DB cluster\. It is a standalone Aurora DB cluster with full read/write capability\.

![\[Screenshot showing confirmation prompt to remove a secondary cluster from an Aurora global database.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-db-detach-secondary-03.png)

After you remove or delete all secondary clusters, then you can remove the primary cluster the same way\. You can't detach \(remove\) the primary Aurora DB cluster from an Aurora global database until after you remove all secondary clusters\. 

The Aurora global database might remain in the **Databases** list, with zero Regions and AZs\. You can delete if you no longer want to use this Aurora global database\. For more information, see [Deleting an Amazon Aurora global database](#aurora-global-database-deleting)\. 

### AWS CLI<a name="aurora-global-database-detach.cli"></a>

 To remove an Aurora cluster from an Aurora global database, run the [remove\-from\-global\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/remove-from-global-cluster.html) CLI command with the following parameters: 
+ `--global-cluster-identifier` – The name \(identifier\) of your Aurora global database\. 
+ `--db-cluster-identifier` – The name of each Aurora DB cluster to remove from the Aurora global database\. Remove all secondary Aurora DB clusters before removing the primary\. 

The following examples first remove a secondary cluster and then the primary cluster from an Aurora global database\. 

For Linux, macOS, or Unix:

```
aws rds --region secondary_region \
  remove-from-global-cluster \
    --db-cluster-identifier secondary_cluster_ARN \
    --global-cluster-identifier global_database_id

aws rds --region primary_region \
  remove-from-global-cluster \
    --db-cluster-identifier primary_cluster_ARN \
    --global-cluster-identifier global_database_id
```

Repeat the `remove-from-global-cluster --db-cluster-identifier secondary_cluster_ARN ` command for each secondary AWS Region in your Aurora global database\. 

For Windows:

```
aws rds --region secondary_region ^
  remove-from-global-cluster ^
    --db-cluster-identifier secondary_cluster_ARN ^
    --global-cluster-identifier global_database_id

aws rds --region primary_region ^
  remove-from-global-cluster ^
    --db-cluster-identifier primary_cluster_ARN ^
    --global-cluster-identifier global_database_id
```

Repeat the `remove-from-global-cluster --db-cluster-identifier secondary_cluster_ARN ` command for each secondary AWS Region in your Aurora global database\. 

### RDS API<a name="aurora-global-database-detach.api"></a>

 To remove an Aurora cluster from an Aurora global database with the RDS API, run the [RemoveFromGlobalCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RemoveFromGlobalCluster.html) action\. 

## Deleting an Amazon Aurora global database<a name="aurora-global-database-deleting"></a>

Because an Aurora global database typically holds business\-critical data, you can't delete the global database and its associated clusters in a single step\. To delete an Aurora global database, do the following: 
+ Remove all secondary DB clusters from the Aurora global database\. Each cluster becomes a standalone Aurora DB cluster\. To learn how, see [Removing a cluster from an Amazon Aurora global database](#aurora-global-database-detaching)\.
+ From each standalone Aurora DB cluster, delete all Aurora Replicas\.
+ Remove the primary DB cluster from the Aurora global database\. This becomes a standalone Aurora DB cluster\.
+ From the Aurora primary DB cluster, first delete all Aurora Replicas, then delete the writer DB instance\. 

Deleting the writer instance from the newly standalone Aurora DB cluster also typically removes the Aurora DB cluster and the Aurora global database\. 

For more general information, see [Deleting a DB instance from an Aurora DB Cluster](USER_DeleteCluster.md#USER_DeleteInstance)\.

To delete an Aurora global database, you can use the AWS Management Console, the AWS CLI, or the RDS API\. 

### Console<a name="aurora-global-database-deleting.console"></a>

**To delete an Aurora global database**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. Choose **Databases** and find the Aurora global database you want to delete in the listing\.

1. Confirm that all clusters are removed from the Aurora global database\. The Aurora global database should show 0 Regions and AZs and a size of 0 clusters\. 

   If the Aurora global database contains any Aurora DB clusters, you can't delete it\. If necessary, detach the primary and secondary Aurora DB clusters from the Aurora global database\. For more information, see [Removing a cluster from an Amazon Aurora global database](#aurora-global-database-detaching)\.

1. Choose your Aurora global database in the list, and then choose **Delete** from the Actions menu\.  
![\[An Aurora global database based on Aurora MySQL 5.6.10a remains in the AWS Management Console until you delete it, even if it doesn't have any associated Aurora DB clusters.\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/aurora-global-db-ams5610a-delete-empty-cluster.png)

### AWS CLI<a name="aurora-global-database-deleting.cli"></a>

To delete an Aurora global database, run the [delete\-global\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/delete-global-cluster.html) CLI command with the name of the AWS Region and the Aurora global database identifier, as shown in the following example\.

For Linux, macOS, or Unix:

```
aws rds --region primary_region delete-global-cluster \
   --global-cluster-identifier global_database_id
```

For Windows:

```
aws rds --region primary_region delete-global-cluster ^
   --global-cluster-identifier global_database_id
```

### RDS API<a name="aurora-global-database-deleting.api"></a>

To delete a cluster that is part of an Aurora global database, run the [DeleteGlobalCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteGlobalCluster.html) API operation\. 