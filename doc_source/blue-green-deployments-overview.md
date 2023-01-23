# Overview of Amazon RDS Blue/Green Deployments for Aurora<a name="blue-green-deployments-overview"></a>

By using Amazon RDS Blue/Green Deployments, you can create a blue/green deployment for managed database changes\. A *blue/green deployment* creates a staging environment that copies the production environment\. In a blue/green deployment, the *blue environment* is the current production environment\. The *green environment* is the staging environment\. The staging environment stays in sync with the current production environment using logical replication\.

You can make changes to the Aurora DB cluster in the green environment without affecting production workloads\. For example, you can upgrade the major or minor DB engine version, change database parameters, or make schema changes in the staging environment\. You can thoroughly test changes in the green environment\. When ready, you can *switch over* the environments to promote the green environment to be the new production environment\. The switchover typically takes under a minute with no data loss and no need for application changes\.

Because the green environment is a copy of the topology of the production environment, the DB cluster and all of its DB instances are copied in the deployment\. The green environment also includes the features used by the DB cluster, such as DB cluster snapshots, Performance Insights, Enhanced Monitoring, and Aurora Serverless v2\.

**Note**  
Currently, blue/green deployments are supported only for Aurora MySQL\.

**Topics**
+ [Benefits of using Amazon RDS Blue/Green Deployments](#blue-green-deployments-benefits)
+ [Workflow of a blue/green deployment](#blue-green-deployments-major-steps)
+ [Authorizing access to blue/green deployment operations](#blue-green-deployments-authorizing-access)
+ [Considerations for blue/green deployments](#blue-green-deployments-considerations)
+ [Best practices for blue/green deployments](#blue-green-deployments-best-practices)
+ [Region and version availability](#blue-green-deployments-region-version-availability)
+ [Limitations for blue/green deployments](#blue-green-deployments-limitations)

## Benefits of using Amazon RDS Blue/Green Deployments<a name="blue-green-deployments-benefits"></a>

By using Amazon RDS Blue/Green Deployments, you can stay current on security patches, improve database performance, and adopt newer database features with short, predictable downtime\. Blue/green deployments reduce the risks and downtime for database updates, such as major or minor engine version upgrades\.

Blue/green deployments provide the following benefits:
+ Easily create a production\-ready staging environment\.
+ Automatically replicate database changes from the production environment to the staging environment\.
+ Test database changes in a safe staging environment without affecting the production environment\.
+ Stay current with database patches and system updates\.
+ Implement and test newer database features\.
+ Switch over your staging environment to be the new production environment without changes to your application\.
+ Safely switch over through the use of built\-in switchover guardrails\.
+ Eliminate data loss during switchover\.
+ Switch over quickly, typically under a minute depending on your workload\.

## Workflow of a blue/green deployment<a name="blue-green-deployments-major-steps"></a>

Complete the following major steps when you use a blue/green deployment for Aurora DB cluster updates\.

1. Identify a production DB cluster that requires updates\.

   The following image shows an example of a production DB cluster\.  
![\[Production (blue) Aurora DB cluster in a blue/green deployment\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/blue-green-deployment-blue-environment-aurora.png)

1. Create the blue/green deployment\. For instructions, see [Creating a blue/green deployment](blue-green-deployments-creating.md)\.

   The following image shows an example of a blue/green deployment of the production environment from step 1\. While creating the blue/green deployment, RDS copies the complete topology and configuration of the Aurora DB cluster to create the green environment\. The names of the copied DB cluster and DB instances are appended with `-green-random-characters`\. The staging environment in the image contains the DB cluster \(auroradb\-green\-**abc123**\)\. It also contains the three DB instances in the DB cluster \(auroradb\-instance1\-green\-**abc123**, auroradb\-instance2\-green\-**abc123**, and auroradb\-instance3\-green\-**abc123**\)\.  
![\[Blue/green deployment for Amazon Aurora\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/blue-green-deployment-aurora.png)

   When you create the blue/green deployment, you can specify a higher DB engine version and a different DB cluster parameter group for the DB cluster in the green environment\. You can also specify a different DB parameter group for the DB instances in the DB cluster\.

   RDS also configures replication from the primary DB instance in the blue environment to the primary DB instance in the green environment\.
**Important**  
After you create the blue/green deployment, the DB cluster in the green environment allows write operations by default\. We recommend that you make the DB cluster read\-only\.

1. Make changes to the staging environment\.

   For example, you might make schema changes to your database or change the DB instance class used by one or more DB instances in the green environment\.

   For information about modifying a DB cluster, see [Modifying an Amazon Aurora DB cluster](Aurora.Modifying.md)\.

1. Test your staging environment\.

   During testing, we recommend that you keep your databases in the green environment read only\. We recommend that you enable write operations on the green environment with caution because they can result in replication conflicts in the green environment\. They can also result in unintended data in the production databases after switchover\.

1. When ready, switch over to promote the staging environment to be the new production environment\. For instructions, see [Switching a blue/green deployment](blue-green-deployments-switching.md)\.

   The switchover results in downtime\. The downtime is usually under one minute, but it can be longer depending on your workload\.

   The following image shows the DB clusters after the switchover\.  
![\[DB cluster and DB instances after switching over an Amazon Aurora blue/green deployment\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/blue-green-deployment-switchover-aurora.png)

   After the switchover, the Aurora DB cluster in the green environment becomes the new production DB cluster\. The names and endpoints in the current production environment are assigned to the newly promoted production environment, requiring no changes to your application\. As a result, your production traffic now flows to the new production environment\. The DB cluster and DB instances in the blue environment are renamed by appending `-oldn` to the current name, where `n` is a number\. For example, assume the name of the DB instance in the blue environment is `auroradb-instance-1`\. After switchover, the DB instance name might be `auroradb-instance-1-old1`\.

   In the example in the image, the following changes occur during switchover:
   + The green environment DB cluster `auroradb-green-abc123` becomes the production DB cluster named `auroradb`\.
   + The green environment DB instance named `auroradb-instance1-green-abc123` becomes the production DB instance `auroradb-instance1`\.
   + The green environment DB instance named `auroradb-instance2-green-abc123` becomes the production DB instance `auroradb-instance2`\.
   + The green environment DB instance named `auroradb-instance3-green-abc123` becomes the production DB instance `auroradb-instance3`\.
   + The blue environment DB cluster named `auroradb` becomes `auroradb-old1`\.
   + The blue environment DB instance named `auroradb-instance1` becomes `auroradb-instance1-old1`\.
   + The blue environment DB instance named `auroradb-instance2` becomes `auroradb-instance2-old1`\.
   + The blue environment DB instance named `auroradb-instance3` becomes `auroradb-instance3-old1`\.

1. If you no longer need a blue/green deployment, you can delete it\. For instructions, see [Deleting a blue/green deployment](blue-green-deployments-deleting.md)\.

   After switchover, the previous production environment isn't deleted so that you can use it for regression testing, if necessary\.

## Authorizing access to blue/green deployment operations<a name="blue-green-deployments-authorizing-access"></a>

Users must have the required permissions to perform operations related to blue/green deployments\. You can create IAM policies that grant users and roles permission to perform specific API operations on the specified resources they need\. You can then attach those policies to the IAM permission sets or roles that require those permissions\. For more information, see [Identity and access management for Amazon Aurora](UsingWithRDS.IAM.md)\.

The user who creates a blue/green deployment must have permissions to perform the following RDS operations:
+ `rds:AddTagsToResource` 
+ `rds:CreateDBCluster` 
+ `rds:CreateDBInstance` 
+ `rds:CreateDBClusterEndpoint` 

The user who switches over a blue/green deployment must have permissions to perform the following RDS operations:
+ `rds:ModifyDBCluster` 
+ `rds:PromoteReadReplicaDBCluster` 

The user who deletes a blue/green deployment must have permissions to perform the following RDS operations:
+ `rds:DeleteDBCluster` 
+ `rds:DeleteDBInstance` 
+ `rds:DeleteDBClusterEndpoint` 

## Considerations for blue/green deployments<a name="blue-green-deployments-considerations"></a>

Amazon RDS tracks resources in blue/green deployments with the `DbiResourceId` `and DbClusterResourceId` \(resource ID\) of each resource\. This resource ID is an AWS Region\-unique, immutable identifier for the resource\.

The *resource* ID is separate from the DB *cluster*** ID:

![\[Create blue/green deployment\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/blue-green-deployment-resource-id-aurora.png)

The name \(cluster ID\) of a resource changes when you switch over a blue/green deployment, but each resource keeps the same resource ID\. For example, a DB cluster identifier might have been `mycluster` in the blue environment\. After switchover, the same DB cluster might be renamed to `mycluster-old1`\. However, the resource ID of the DB cluster doesn't change during switchover\. So, when the green resources are promoted to be the new production resources, their resource IDs don't match the blue resource IDs that were previously in production\.

After switching over a blue/green deployment, consider updating the resource IDs to those of the newly promoted production resources for integrated features and services that you used with the production resources\. Specifically, consider the following updates:
+ If you perform filtering using the RDS API and resource IDs, adjust the resource IDs used in filtering after switchover\.
+ If you use CloudTrail for auditing resources, adjust the consumers of the CloudTrail to track the new resource IDs after switchover\. For more information, see [Monitoring Amazon Aurora API calls in AWS CloudTrail](logging-using-cloudtrail.md)\.
+ If you use Database Activity Streams for resources in the blue environment, adjust your application to monitor database events for the new stream after switchover\. For more information, see [Database activity streams in Aurora](Concepts.Aurora_Fea_Regions_DB-eng.Feature.DBActivityStreams.md)\.
+ If you use the Performance Insights API, adjust the resource IDs in calls to the API after switchover\. For more information, see [Monitoring DB load with Performance Insights on Amazon Aurora](USER_PerfInsights.md)\.

  You can monitor a database with the same name after switchover, but it doesn't contain the data from before the switchover\.
+ If you use resource IDs in IAM policies, make sure you add the resource IDs of the newly promoted resources when necessary\. For more information, see [Identity and access management for Amazon Aurora](UsingWithRDS.IAM.md)\.
+ If you want to restore a manual DB cluster snapshot for a DB cluster that was part of a blue/green deployment, make sure you restore the correct DB cluster snapshot by examining the time when the snapshot was taken\. For more information, see [Restoring from a DB cluster snapshot](aurora-restore-snapshot.md)\.
+ When you add a DB instance to the DB cluster in the green environment of a blue/green deployment, the new DB instance won't replace a DB instance in the blue environment when you switch over\. However, the new DB instance is retained in the DB cluster and becomes a DB instance in the new production environment\.
+ When you delete a DB instance in the DB cluster in the green environment of a blue/green deployment, you can't create a new DB instance to replace it in the blue/green deployment\.

  If you create a new DB instance with the same name and ARN as the deleted DB instance, it has a different `DbiResourceId`, so it isn't part of the green environment\.

  The following behavior results if you delete a DB instance in the DB cluster in the green environment:
  + If the DB instance in the blue environment with the same name exists, it won't be switched over to the DB instance in the green environment\. This DB instance won't be renamed by appending `-oldn` to the DB instance name\.
  + Any application that points to the DB instance in the blue environment continues to use the same DB instance after switchover\.

## Best practices for blue/green deployments<a name="blue-green-deployments-best-practices"></a>

The following are best practices for blue/green deployments:
+ Thoroughly test the Aurora DB cluster in the green environment before switching over\.
+ Keep your databases in the green environment read only\. We recommend that you enable write operations on the green environment with caution because they can result in replication conflicts in the green environment\. They can also result in unintended data in the production databases after switchover\.
+ Identify the best time for the switchover\.

  During the switchover, writes are cut off from databases in both environments\. Identify a time when traﬃc is lowest on your production environment\. Long\-running transactions, such as active DDLs, can increase your switchover time, resulting in longer downtime for your production workloads\.
+ When using a blue/green deployment to implement schema changes, make only replication\-compatible changes\.

  For example, you can add new columns at the end of a table, create indexes, or drop indexes without disrupting replication from the blue deployment to the green deployment\. However, schema changes, such as renaming columns or renaming tables, break binary log replication to the green deployment\.

  For more information about replication\-compatible changes, see [ Replication with Differing Table Definitions on Source and Replica](https://dev.mysql.com/doc/refman/8.0/en/replication-features-differing-tables.html) in the MySQL documentation\.
+ Use the cluster endpoint, reader endpoint, or custom endpoint for all connections in both environments\. Don't use instance endpoints or custom endpoints with static or exclusion lists\.

## Region and version availability<a name="blue-green-deployments-region-version-availability"></a>

Feature availability and support varies across specific versions of each database engine, and across AWS Regions\. For more information on version and Region availability with Amazon RDS Blue/Green Deployments, see [Blue/Green Deployments](Concepts.Aurora_Fea_Regions_DB-eng.Feature.BlueGreenDeployments.md)\. 

## Limitations for blue/green deployments<a name="blue-green-deployments-limitations"></a>

The following limitations apply to blue/green deployments:
+ Blue/green deployments aren't supported for the following features:
  + Amazon RDS Proxy
  + Cross\-Region read replicas
  + Aurora Serverless v1 DB clusters
  + Aurora multi\-master clusters
  + DB clusters that are part of an Aurora global database

    If an Aurora MySQL version 5\.6\.10a DB cluster was part of an Aurora global database, the DB cluster can't be included in a blue/green deployment even if the DB cluster was removed from the Aurora global database\. To include the DB cluster in a blue/green deployment, upgrade the DB cluster to a higher Aurora MySQL version\.
  + AWS CloudFormation
+ The following are limitations for changes in a blue/green deployment:
  + Aurora MySQL version 3 \(compatible with MySQL 8\.0\) DB clusters aren't supported in the blue environment, but they are supported in the green environment\.
  + You can't change an unencrypted DB cluster into an encrypted DB cluster\.
  + You can't change an encrypted DB cluster into an unencrypted DB cluster\.
  + You can't change a blue environment DB cluster to a higher engine version than its corresponding green environment DB cluster\.
  + The resources in the blue environment and green environment must be in the same AWS account\.
  + During switchover, the blue DB cluster can't be the target of external replication\.