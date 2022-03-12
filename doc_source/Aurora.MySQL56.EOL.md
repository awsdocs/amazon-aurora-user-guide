# Preparing for Amazon Aurora MySQL\-Compatible Edition version 1 end of life<a name="Aurora.MySQL56.EOL"></a>

Amazon Aurora MySQL\-Compatible Edition version 1 \(with MySQL 5\.6 compatibility\) is planned to reach end of life on February 28, 2023\. Amazon advises that you upgrade all clusters \(provisioned and Aurora Serverless v1\) running Aurora MySQL version 1 to Aurora MySQL version 2 \(with MySQL 5\.7 compatibility\) or Aurora MySQL version 3 \(with MySQL 8\.0 compatibility\)\. Do this before Aurora MySQL version 1 reaches the end of its support period\.

For Aurora provisioned DB clusters, you can complete upgrades from Aurora MySQL version 1 to Aurora MySQL version 2 by several methods\. You can find instructions for the in\-place upgrade mechanism in [How to perform an in\-place upgrade](AuroraMySQL.Updates.MajorVersionUpgrade.md#AuroraMySQL.Upgrading.Procedure)\. Another way to complete the upgrade is to take a snapshot of an Aurora MySQL version 1 cluster and restore the snapshot to an Aurora MySQL version 2 cluster\. Or you can follow a multistep process that runs the old and new clusters side by side\. For more details about each method, see [Upgrading from Aurora MySQL 1\.x to 2\.x](AuroraMySQL.Updates.MajorVersionUpgrade.md#AuroraMySQL.Updates.MajorVersionUpgrade.1to2)

For Aurora Serverless v1 DB clusters, you can complete upgrades from Aurora MySQL version 1 to Aurora MySQL version 2 by taking a snapshot of an Aurora MySQL version 1 cluster and restoring the snapshot to an Aurora MySQL version 2 cluster\. For more details about this method, see [ Restoring an Aurora Serverless v1 DB cluster](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-serverless.restorefromsnapshot.html)\.

For Aurora provisioned DB clusters, you can complete upgrades from Aurora MySQL version 1 to Aurora MySQL version 3 by using a two\-stage upgrade process\. The first stage requires an upgrade from Aurora MySQL version 1 to Aurora MySQL version 2 using the methods described preceding\. The second stage requires an upgrade from Aurora MySQL version 2 to Aurora MySQL version 3\. To perform this upgrade, take a snapshot of an Aurora MySQL version 2 cluster and restore the snapshot to an Aurora MySQL version 3 cluster\. For more details, see [Upgrading from Aurora MySQL 2\.x to 3\.x](AuroraMySQL.Updates.MajorVersionUpgrade.md#AuroraMySQL.Updates.MajorVersionUpgrade.2to3)\. 

You can find upcoming end\-of\-life dates for Aurora major versions in [Amazon Aurora versions](Aurora.VersionPolicy.md)\. Amazon automatically upgrades any clusters that you don't upgrade yourself before the end\-of\-life date\. After the end\-of\-life date, these automatic upgrades to the subsequent major version occur during a scheduled maintenance window for clusters\.  

The following are additional milestones for upgrading Aurora MySQL version 1 clusters \(provisioned and Aurora Serverless v1\) that are reaching end of life\. For each, the start time is 00:00 Universal Coordinated Time \(UTC\)\. 

1. Now through February 28, 2023 – You can at any time start upgrades of Aurora MySQL version 1 \(with MySQL 5\.6 compatibility\) clusters to Aurora MySQL version 2 \(with MySQL 5\.7 compatibility\)\. From Aurora MySQL version 2, you can do a further upgrade to Aurora MySQL version 3 \(with MySQL 8\.0 compatibility\) for Aurora provisioned DB clusters\. 

1. September 27, 2022 – After this time, you can't create new Aurora MySQL version 1 clusters or instances from either the AWS Management Console or the AWS Command Line Interface \(AWS CLI\)\. You also can't add new secondary Regions to an Aurora global database\. This might affect your ability to recover from an unplanned outage as outlined in [Recovering an Amazon Aurora global database from an unplanned outage](aurora-global-database-disaster-recovery.md#aurora-global-database-failover), because you can't complete steps 5 and 6 after this time\. You can still do the following for existing Aurora MySQL version 1 clusters until February 28, 2023:
   + Restore a snapshot taken of an Aurora MySQL version 1 cluster\.
   + Add read replicas \(not applicable for Aurora Serverless v1 DB clusters\)\.
   + Change instance configuration\.
   + Perform point\-in\-time restore\.
   + Create clones of existing version 1 clusters\.

1.  February 28, 2023 – After this time, we plan to automatically upgrade Aurora MySQL version 1 clusters to the default version of Aurora MySQL version 2 within a scheduled maintenance window that follows\. Restoring Aurora MySQL version 1 DB snapshots results in an automatic upgrade of the restored cluster to the default version of Aurora MySQL version 2 at that time\. 

Upgrading between major versions requires more extensive planning and testing than for a minor version\. The process can take substantial time\. After the upgrade is finished, you also might have follow\-up work to do\. For example, you might need to follow up due to differences in SQL compatibility, the way certain MySQL\-related features work, or parameter settings between the old and new versions\. 

To learn more about the methods, planning, testing, and troubleshooting of Aurora MySQL major version upgrades, be sure to thoroughly read [Upgrading the major version of an Aurora MySQL DB cluster](AuroraMySQL.Updates.MajorVersionUpgrade.md) \. 

## Finding clusters affected by this end\-of\-life process<a name="find-cluster"></a>

To find clusters affected by this end\-of\-life process, use the following procedures\.

**Important**  
Be sure to perform these instructions in every AWS Region where your resources are located\.

### Console<a name="aurora-find-mysqlv1-cluster.CON"></a>

**To find an Aurora MySQL version 1 cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  In the navigation pane, choose **Databases**\.

1.  In the **Filter by databases** box, enter **5\.6**\.

1. Check for Aurora MySQL in the engine column\.

### AWS CLI<a name="aurora-find-mysqlv1-cluster.CLI"></a>

To find clusters affected by this end\-of\-life process using the AWS CLI, call the [describe\-db\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html) command\. You can use the sample script following\.

**Example**  

```
aws rds describe-db-clusters --include-share --query 'DBClusters[?Engine==`aurora`].{EV:EngineVersion, DBCI:DBClusterIdentifier, EM:EngineMode}' --output table --region us-east-1     
        
        +------------------------------------------+
        |            DescribeDBClusters            |
        +---------------+--------------+-----------+
        |     DBCI      |     EM       |    EV     |
        +---------------+--------------+-----------+
        |  my-database-1|  serverless  |  5.6.10a  |
        +---------------+--------------+-----------+
```

### RDS API<a name="Aurora-find-mysqlv1-cluster.API"></a>

To find Aurora MySQL DB clusters running Aurora MySQL version 1, use the RDS [DescribeDBClusters](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBClusters.html) API operation with the following required parameters: 
+  `DescribeDBClusters`
  + Filters\.Filter\.N
    + Name
      + engine
    + Values\.Value\.N
      + \['aurora'\]