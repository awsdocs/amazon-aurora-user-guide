# Viewing key monitoring information<a name="accessing-monitoring"></a>

The Amazon RDS console provides quick access to key monitoring features\.

**Topics**
+ [Viewing an Amazon Aurora DB cluster](#Aurora.Viewing)
+ [Viewing DB cluster status](#Aurora.Status)
+ [Viewing DB instance status](#Overview.DBInstance.Status)
+ [Viewing Amazon Aurora recommendations](#USER_Recommendations)
+ [Viewing DB instance metrics](#USER_Monitoring)

## Viewing an Amazon Aurora DB cluster<a name="Aurora.Viewing"></a>

You have several options for viewing information about your Amazon Aurora DB clusters and the DB instances in your DB clusters\.
+ You can view DB clusters and DB instances in the Amazon RDS console by choosing **Databases** from the navigation pane\. 
+ You can get DB cluster and DB instance information using the AWS Command Line Interface \(AWS CLI\)\.
+ You can get DB cluster and DB instance information using the Amazon RDS API\.

### Console<a name="Aurora.Viewing.Console"></a>

In the Amazon RDS console, you can see details about a DB cluster by choosing **Databases** from the console's navigation pane\. You can also see details about DB instances that are members of an Amazon Aurora DB cluster on the **Databases** page\.

The **Databases** list shows all of the DB clusters for your AWS account\. When you choose a DB cluster, you see both information about the DB cluster and also a list of the DB instances that are members of that DB cluster\. You can choose the identifier for a DB instance in the list to go directly to the details page for that DB instance in the RDS console\.

To view the details page for a DB cluster, choose **Databases** in the navigation pane, and then choose the name of the DB cluster\.

You can modify your DB cluster by choosing **Databases** from the console's navigation pane to go to the **Databases** list\. To modify a DB cluster, select the DB cluster from the **Databases** list and choose **Modify**\.

To modify a DB instance that is a member of a DB cluster, choose **Databases** from the console's navigation pane to go to the **Databases** list\.

For example, the following image shows the details page for the DB cluster named `aurora-test`\. The DB cluster has four DB instances shown in the **DB identifier** list\. The writer DB instance, `dbinstance4`, is the primary DB instance for the DB cluster\.

![\[Amazon Aurora DB Cluster View\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraView01.png)

If you click the link for the `dbinstance4` DB instance identifier, the Amazon RDS console shows the details page for the `dbinstance4` DB instance, as shown in the following image\.

![\[Amazon Aurora DB Instance View\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraView02.png)

### AWS CLI<a name="Aurora.Viewing.CLI"></a>

To view DB cluster information by using the AWS CLI, use the [describe\-db\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html) command\. For example, the following AWS CLI command lists the DB cluster information for all of the DB clusters in the `us-east-1` region for the configured AWS account\.

```
aws rds describe-db-clusters --region us-east-1
```

The command returns the following output if your AWS CLI is configured for JSON output\.

```
{
    "DBClusters": [
        {
            "Status": "available",
            "Engine": "aurora",
            "Endpoint": "sample-cluster1.cluster-123456789012.us-east-1.rds.amazonaws.com"
            "AllocatedStorage": 1,
            "DBClusterIdentifier": "sample-cluster1",
            "MasterUsername": "mymasteruser",
            "EarliestRestorableTime": "2016-03-30T03:35:42.563Z",
            "DBClusterMembers": [
                {
                    "IsClusterWriter": false,
                    "DBClusterParameterGroupStatus": "in-sync",
                    "DBInstanceIdentifier": "sample-replica"
                },
                {
                    "IsClusterWriter": true,
                    "DBClusterParameterGroupStatus": "in-sync",
                    "DBInstanceIdentifier": "sample-primary"
                }
            ],
            "Port": 3306,
            "PreferredBackupWindow": "03:34-04:04",
            "VpcSecurityGroups": [
                {
                    "Status": "active",
                    "VpcSecurityGroupId": "sg-ddb65fec"
                }
            ],
            "DBSubnetGroup": "default",
            "StorageEncrypted": false,
            "DatabaseName": "sample",
            "EngineVersion": "5.6.10a",
            "DBClusterParameterGroup": "default.aurora5.6",
            "BackupRetentionPeriod": 1,
            "AvailabilityZones": [
                "us-east-1b",
                "us-east-1c",
                "us-east-1d"
            ],
            "LatestRestorableTime": "2016-03-31T20:06:08.903Z",
            "PreferredMaintenanceWindow": "wed:08:15-wed:08:45"
        },
        {
            "Status": "available",
            "Engine": "aurora",
            "Endpoint": "aurora-sample.cluster-123456789012.us-east-1.rds.amazonaws.com",
            "AllocatedStorage": 1,
            "DBClusterIdentifier": "aurora-sample-cluster",
            "MasterUsername": "mymasteruser",
            "EarliestRestorableTime": "2016-03-30T10:21:34.826Z",
            "DBClusterMembers": [
                {
                    "IsClusterWriter": false,
                    "DBClusterParameterGroupStatus": "in-sync",
                    "DBInstanceIdentifier": "aurora-replica-sample"
                },
                {
                    "IsClusterWriter": true,
                    "DBClusterParameterGroupStatus": "in-sync",
                    "DBInstanceIdentifier": "aurora-sample"
                }
            ],
            "Port": 3306,
            "PreferredBackupWindow": "10:20-10:50",
            "VpcSecurityGroups": [
                {
                    "Status": "active",
                    "VpcSecurityGroupId": "sg-55da224b"
                }
            ],
            "DBSubnetGroup": "default",
            "StorageEncrypted": false,
            "DatabaseName": "sample",
            "EngineVersion": "5.6.10a",
            "DBClusterParameterGroup": "default.aurora5.6",
            "BackupRetentionPeriod": 1,
            "AvailabilityZones": [
                "us-east-1b",
                "us-east-1c",
                "us-east-1d"
            ],
            "LatestRestorableTime": "2016-03-31T20:00:11.491Z",
            "PreferredMaintenanceWindow": "sun:03:53-sun:04:23"
        }
    ]
}
```

### RDS API<a name="Aurora.Viewing.API"></a>

To view DB cluster information using the Amazon RDS API, use the [DescribeDBClusters](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBClusters.html) operation\. For example, the following Amazon RDS API command lists the DB cluster information for all of the DB clusters in the `us-east-1` region\.

```
https://rds.us-east-1.amazonaws.com/
    ?Action=DescribeDBClusters
    &MaxRecords=100
    &SignatureMethod=HmacSHA256
    &SignatureVersion=4
    &Version=2014-10-31
    &X-Amz-Algorithm=AWS4-HMAC-SHA256
    &X-Amz-Credential=AKIADQKE4SARGYLE/20140722/us-east-1/rds/aws4_request
    &X-Amz-Date=20140722T200807Z
    &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
    &X-Amz-Signature=2d4f2b9e8abc31122b5546f94c0499bba47de813cb875f9b9c78e8e19c9afe1b
```

The action returns the following output:

```
<DescribeDBClustersResponse xmlns="http://rds.amazonaws.com/doc/2014-10-31/">
  <DescribeDBClustersResult>
    <DBClusters>
      <DBCluster>
        <Engine>aurora5.6</Engine>
        <Status>available</Status>
        <BackupRetentionPeriod>0</BackupRetentionPeriod>
        <DBSubnetGroup>my-subgroup</DBSubnetGroup>
        <EngineVersion>5.6.10a</EngineVersion>
        <Endpoint>sample-cluster2.cluster-cbfvmgb0y5fy.us-east-1.rds.amazonaws.com</Endpoint>
        <DBClusterIdentifier>sample-cluster2</DBClusterIdentifier>
        <PreferredBackupWindow>04:45-05:15</PreferredBackupWindow>
        <PreferredMaintenanceWindow>sat:05:56-sat:06:26</PreferredMaintenanceWindow>
        <DBClusterMembers/>
        <AllocatedStorage>15</AllocatedStorage>
        <MasterUsername>awsuser</MasterUsername>
      </DBCluster>
      <DBCluster>
        <Engine>aurora5.6</Engine>
        <Status>available</Status>
        <BackupRetentionPeriod>0</BackupRetentionPeriod>
        <DBSubnetGroup>my-subgroup</DBSubnetGroup>
        <EngineVersion>5.6.10a</EngineVersion>
        <Endpoint>sample-cluster3.cluster-cefgqfx9y5fy.us-east-1.rds.amazonaws.com</Endpoint>
        <DBClusterIdentifier>sample-cluster3</DBClusterIdentifier>
        <PreferredBackupWindow>07:06-07:36</PreferredBackupWindow>
        <PreferredMaintenanceWindow>tue:10:18-tue:10:48</PreferredMaintenanceWindow>
        <DBClusterMembers>
          <DBClusterMember>
            <IsClusterWriter>true</IsClusterWriter>
            <DBInstanceIdentifier>sample-cluster3-master</DBInstanceIdentifier>
          </DBClusterMember>
          <DBClusterMember>
            <IsClusterWriter>false</IsClusterWriter>
            <DBInstanceIdentifier>sample-cluster3-read1</DBInstanceIdentifier>
          </DBClusterMember>
        </DBClusterMembers>
        <AllocatedStorage>15</AllocatedStorage>
        <MasterUsername>awsuser</MasterUsername>
      </DBCluster>
    </DBClusters>
  </DescribeDBClustersResult>
  <ResponseMetadata>
    <RequestId>d682b02c-1383-11b4-a6bb-172dfac7f170</RequestId>
  </ResponseMetadata>
</DescribeDBClustersResponse>
```

## Viewing DB cluster status<a name="Aurora.Status"></a>

The status of a DB cluster indicates its health\. You can view the status of a DB cluster by using the Amazon RDS console, the AWS CLI command [describe\-db\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html), or the API operation [DescribeDBClusters](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBClusters.html)\.

**Note**  
Aurora also uses another status called *maintenance status*, which is shown in the **Maintenance** column of the Amazon RDS console\. This value indicates the status of any maintenance patches that need to be applied to a DB cluster\. Maintenance status is independent of DB cluster status\. For more information on *maintenance status*, see [Applying updates for a DB cluster](USER_UpgradeDBInstance.Maintenance.md#USER_UpgradeDBInstance.OSUpgrades)\.

Find the possible status values for DB clusters in the following table\.


| DB cluster status | Billed | Description | 
| --- | --- | --- | 
| available | Billed |  The DB cluster is healthy and available\. When an Aurora Serverless cluster is available and paused, you're billed for storage only\.  | 
| backing\-up | Billed |  The DB cluster is currently being backed up\.  | 
| backtracking | Billed |  The DB cluster is currently being backtracked\. This status only applies to Aurora MySQL\.  | 
| cloning\-failed | Not billed |  Cloning a DB cluster failed\.  | 
| creating | Not billed |  The DB cluster is being created\. The DB cluster is inaccessible while it is being created\.  | 
| deleting | Not billed |  The DB cluster is being deleted\.  | 
| failing\-over | Billed |  A failover from the primary instance to an Aurora Replica is being performed\.  | 
| inaccessible\-encryption\-credentials | Not billed |  The AWS KMS key used to encrypt or decrypt the DB cluster can't be accessed\.  | 
| maintenance | Billed |  Amazon RDS is applying a maintenance update to the DB cluster\. This status is used for DB cluster\-level maintenance that RDS schedules well in advance\.  | 
| migrating | Billed |  A DB cluster snapshot is being restored to a DB cluster\.  | 
| migration\-failed | Not billed |  A migration failed\.  | 
| modifying | Billed |  The DB cluster is being modified because of a customer request to modify the DB cluster\.  | 
| promoting | Billed |  A read replica is being promoted to a standalone DB cluster\.  | 
| renaming | Billed |  The DB cluster is being renamed because of a customer request to rename it\.  | 
| resetting\-master\-credentials | Billed |  The master credentials for the DB cluster are being reset because of a customer request to reset them\.  | 
| starting | Billed for storage |  The DB cluster is starting\.  | 
| stopped | Billed for storage |  The DB cluster is stopped\.  | 
| stopping | Billed for storage |  The DB cluster is being stopped\.  | 
| update\-iam\-db\-auth | Billed |  IAM authorization for the DB cluster is being updated\.  | 
| upgrading | Billed |  The DB cluster engine version is being upgraded\.  | 

## Viewing DB instance status<a name="Overview.DBInstance.Status"></a>

The status of a DB instance in an Aurora cluster indicates the health of the DB instance\. You can view the status of a DB instance in a cluster by using the Amazon RDS console, the AWS CLI command [describe\-db\-instances](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-instances.html), or the API operation [DescribeDBInstances](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBInstances.html)\. 

**Note**  
Amazon RDS also uses another status called *maintenance status*, which is shown in the **Maintenance** column of the Amazon RDS console\. This value indicates the status of any maintenance patches that need to be applied to a DB instance\. Maintenance status is independent of DB instance status\. For more information on *maintenance status*, see [Applying updates for a DB cluster](USER_UpgradeDBInstance.Maintenance.md#USER_UpgradeDBInstance.OSUpgrades)\. 

Find the possible status values for DB instances in the following table\. This table also shows whether you will be billed for the DB instance and storage, billed only for storage, or not billed\. For all DB instance statuses, you are always billed for backup usage\.


| DB instance status | Billed  | Description | 
| --- | --- | --- | 
|  **available**  | Billed |  The DB instance is healthy and available\.  | 
|  **backing\-up**  | Billed |  The DB instance is currently being backed up\.  | 
| backtracking | Billed |  The DB instance is currently being backtracked\. This status only applies to Aurora MySQL\.  | 
|  **configuring\-enhanced\-monitoring**  | Billed |  Enhanced Monitoring is being enabled or disabled for this DB instance\.  | 
|  **configuring\-iam\-database\-auth**  | Billed |  AWS Identity and Access Management \(IAM\) database authentication is being enabled or disabled for this DB instance\.  | 
|  **configuring\-log\-exports**  | Billed |  Publishing log files to Amazon CloudWatch Logs is being enabled or disabled for this DB instance\.  | 
|  **converting\-to\-vpc**  | Billed |  The DB instance is being converted from a DB instance that is not in an Amazon Virtual Private Cloud \(Amazon VPC\) to a DB instance that is in an Amazon VPC\.  | 
|  **creating**  | Not billed |  The DB instance is being created\. The DB instance is inaccessible while it is being created\.   | 
|  **deleting**  | Not billed |  The DB instance is being deleted\.  | 
|  **failed**  | Not billed |  The DB instance has failed and Amazon RDS can't recover it\. Perform a point\-in\-time restore to the latest restorable time of the DB instance to recover the data\.   | 
|  **inaccessible\-encryption\-credentials**  | Not billed |  The AWS KMS key used to encrypt or decrypt the DB instance can't be accessed\.   | 
|  **incompatible\-network**  | Not billed |  Amazon RDS is attempting to perform a recovery action on a DB instance but can't do so because the VPC is in a state that prevents the action from being completed\. This status can occur if, for example, all available IP addresses in a subnet are in use and Amazon RDS can't get an IP address for the DB instance\.   | 
|  **incompatible\-option\-group**  | Billed |  Amazon RDS attempted to apply an option group change but can't do so, and Amazon RDS can't roll back to the previous option group state\. For more information, check the **Recent Events** list for the DB instance\. This status can occur if, for example, the option group contains an option such as TDE and the DB instance doesn't contain encrypted information\.   | 
|  **incompatible\-parameters**  | Billed |  Amazon RDS can't start the DB instance because the parameters specified in the DB instance's DB parameter group aren't compatible with the DB instance\. Revert the parameter changes or make them compatible with the DB instance to regain access to your DB instance\. For more information about the incompatible parameters, check the **Recent Events** list for the DB instance\.   | 
|  **incompatible\-restore**  | Not billed |  Amazon RDS can't do a point\-in\-time restore\. Common causes for this status include using temp tables or using MyISAM tables with MySQL\.  | 
| insufficient\-capacity |  |  Amazon RDS can’t create your instance because sufficient capacity isn’t currently available\. To create your DB instance in the same AZ with the same instance type, delete your DB instance, wait a few hours, and try to create again\. Alternatively, create a new instance using a different instance class or AZ\.  | 
|  **maintenance**  | Billed |  Amazon RDS is applying a maintenance update to the DB instance\. This status is used for instance\-level maintenance that RDS schedules well in advance\.   | 
|  **modifying**  | Billed |  The DB instance is being modified because of a customer request to modify the DB instance\.   | 
|  **moving\-to\-vpc**  | Billed |  The DB instance is being moved to a new Amazon Virtual Private Cloud \(Amazon VPC\)\.  | 
|  **rebooting**  | Billed |  The DB instance is being rebooted because of a customer request or an Amazon RDS process that requires the rebooting of the DB instance\.  | 
|  **resetting\-master\-credentials**  | Billed |  The master credentials for the DB instance are being reset because of a customer request to reset them\.  | 
|  **renaming**  | Billed |  The DB instance is being renamed because of a customer request to rename it\.   | 
|  **restore\-error**  | Billed |  The DB instance encountered an error attempting to restore to a point\-in\-time or from a snapshot\.  | 
|  **starting**  | Billed for storage |  The DB instance is starting\.  | 
|  **stopped**  | Billed for storage |  The DB instance is stopped\.  | 
|  **stopping**  | Billed for storage |  The DB instance is being stopped\.  | 
|  **storage\-full**  | Billed |  The DB instance has reached its storage capacity allocation\. This is a critical status, and we recommend that you fix this issue immediately\. To do so, scale up your storage by modifying the DB instance\. To avoid this situation, set Amazon CloudWatch alarms to warn you when storage space is getting low\.   | 
|  **storage\-optimization**  | Billed |  Your DB instance is being modified to change the storage size or type\. The DB instance is fully operational\. However, while the status of your DB instance is **storage\-optimization**, you can't request any changes to the storage of your DB instance\. The storage optimization process is usually short, but can sometimes take up to and even beyond 24 hours\.   | 
|  **upgrading**  | Billed |  The database engine version is being upgraded\.   | 

## Viewing Amazon Aurora recommendations<a name="USER_Recommendations"></a>

Amazon Aurora provides automated recommendations for database resources, such as DB instances, DB clusters, and DB cluster parameter groups\. These recommendations provide best practice guidance by analyzing DB cluster configuration,  DB instance configuration, usage, and performance data\.

You can find examples of these recommendations in the following table\.


| Type | Description | Recommendation | Additional information | 
| --- | --- | --- | --- | 
|  Nondefault custom memory parameters  |  Your DB parameter group sets memory parameters that diverge too much from the default values\.  |  Settings that diverge too much from the default values can cause poor performance and errors\. We recommend setting custom memory parameters to their default values in the DB parameter group used by the DB instance\.  |  [Working with DB parameter groups and DB cluster parameter groups](USER_WorkingWithParamGroups.md)  | 
|  Change buffering enabled for a MySQL DB instance  |  Your DB parameter group has change buffering enabled\.  |  Change buffering allows a MySQL DB instance to defer some writes necessary to maintain secondary indexes\. This configuration can improve performance slightly, but it can create a large delay in crash recovery\. During crash recovery, the secondary index must be brought up to date\. So, the benefits of change buffering are outweighed by the potentially very long crash recovery events\. We recommend disabling change buffering\.  |  [ Best practices for configuring parameters for Amazon RDS for MySQL, part 1: Parameters related to performance](http://aws.amazon.com/blogs/database/best-practices-for-configuring-parameters-for-amazon-rds-for-mysql-part-1-parameters-related-to-performance/) on the AWS Database Blog  | 
|  Logging to table  |  Your DB parameter group sets logging output to `TABLE`\.  |  Setting logging output to `TABLE` uses more storage than setting this parameter to `FILE`\. To avoid reaching the storage limit, we recommend setting the logging output parameter to `FILE`\.  |  [Aurora MySQL database log files](USER_LogAccess.Concepts.MySQL.md)  | 
|  DB cluster with one DB instance  |  Your DB cluster only contains one DB instance\.  |  For improved performance and availability, we recommend adding another DB instance with the same DB instance class in a different Availability Zone\.  |  [High availability for Amazon Aurora](Concepts.AuroraHighAvailability.md)  | 
|  DB cluster in one Availability Zone  |  Your DB cluster has all of its DB instances in the same Availability Zone\.  |  For improved availability, we recommend adding another DB instance with the same DB instance class in a different Availability Zone\.  |  [High availability for Amazon Aurora](Concepts.AuroraHighAvailability.md)  | 
|  DB cluster outdated  |  Your DB cluster is running an older engine version\.  |  We recommend that you keep your DB cluster at the most current minor version because it includes the latest security and functionality fixes\. Unlike major version upgrades, minor version upgrades include only changes that are backward\-compatible with previous minor versions \(of the same major version\) of the DB engine\. We recommend that you upgrade to a recent engine version  |  [Maintaining an Amazon Aurora DB cluster](USER_UpgradeDBInstance.Maintenance.md)  | 
|  DB cluster with different parameter groups  |  Your DB cluster has different DB parameter groups assigned to its DB instances\.  |  Using different parameter groups can cause incompatibilities between the DB instances\. To avoid problems and for easier maintenance, we recommend using the same parameter group for all of the DB instances in the DB cluster\.  |  [Working with DB parameter groups and DB cluster parameter groups](USER_WorkingWithParamGroups.md)  | 
|  DB cluster with different DB instance classes  |  Your DB cluster has DB instances that use different DB instance classes\.  |  Using different DB instance classes for DB instances can cause problems\. For example, performance might suffer if a less powerful DB instance class is promoted to replace a more powerful DB instance class\. To avoid problems and for easier maintenance, we recommend using the same DB instance class for all of the DB instances in the DB cluster\.  |  [Aurora Replicas](Aurora.Replication.md#Aurora.Replication.Replicas)  | 

Amazon Aurora generates recommendations for a resource when the resource is created or modified\. Amazon Aurora also periodically scans your resources and generates recommendations\.

### Responding to Amazon Aurora recommendations<a name="USER_Recommendations.Responding"></a>

You can find recommendations in the AWS Management Console\. You can perform the recommended action immediately, schedule it for the next maintenance window, or dismiss it\.

**To respond to Amazon Aurora recommendations**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Recommendations**\.  
![\[Select Recommendations in the console\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/recommendations-select.png)

   The Recommendations page appears\.  
![\[Main Recommendations page in the console\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/recommendations-main-aurora.png)

1. On the **Recommendations** page, choose one of the following:
   + **Active** – Shows the current recommendations that you can apply, dismiss, or schedule\.
   + **Dismissed** – Shows the recommendations that have been dismissed\. When you choose **Dismissed**, you can apply these dismissed recommendations\.
   + **Scheduled** – Shows the recommendations that are scheduled but not yet applied\. These recommendations will be applied in the next scheduled maintenance window\.
   + **Applied** – Shows the recommendations that are currently applied\.

   From any list of recommendations, you can open a section to view the recommendations in that section\.  
![\[Take action on recommendations in the console\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/recommendations-active-aurora.png)

   To configure preferences for displaying recommendations in each section, choose the **Preferences** icon\.  
![\[Preferences icon for Recommendations in the console\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/recommendations-preferences-aurora.png)

   From the **Preferences** window that appears, you can set display options\. These options include the visible columns and the number of recommendations to display on the page\.

1. Manage your active recommendations:

   1. Choose **Active** and open one or more sections to view the recommendations in them\.

   1. Choose one or more recommendations and choose **Apply now** \(to apply them immediately\), **Schedule** \(to apply them in next maintenance window\), or **Dismiss**\.

      If the **Apply now** button appears for a recommendation but is unavailable \(grayed out\), the DB instance is not available\. You can apply recommendations immediately only if the DB instance status is **available**\. For example, you can't apply recommendations immediately to the DB instance if its status is **modifying**\. In this case, wait for the DB instance to be available and then apply the recommendation\.

      If the **Apply now** button doesn't appear for a recommendation, you can't apply the recommendation using the **Recommendations** page\. You can modify the DB instance to apply the recommendation manually\.

      For more information about modifying a DB cluster, see [Modifying an Amazon Aurora DB cluster](Aurora.Modifying.md)\.
**Note**  
When you choose **Apply now**, a brief DB instance outage might result\.

## Viewing DB instance metrics<a name="USER_Monitoring"></a>

Amazon RDS provides metrics so that you can monitor the health of your DB instances\. You can monitor both DB instance metrics and operating system \(OS\) metrics\.

Following, you can find details about how to view metrics for your DB instance using the RDS console and CloudWatch\. For information on monitoring metrics for your DB instance's operating system in real time using CloudWatch Logs, see [Monitoring the OS by using Enhanced Monitoring](USER_Monitoring.OS.md)\.

### Viewing metrics by using the console<a name="USER_Monitoring.CON"></a>

**To view DB and OS metrics for a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the name of the DB instance that you need information about to show its details\.

1. Choose the **Monitoring** tab\.

1. For **Monitoring**, choose the option for how you want to view your metrics from these:
   + **CloudWatch** – Shows a summary of DB instance metrics available from Amazon CloudWatch\. Each metric includes a graph showing the metric monitored over a specific time span\.
   + **Enhanced monitoring** – Shows a summary of OS metrics available for a DB instance with Enhanced Monitoring enabled\. Each metric includes a graph showing the metric monitored over a specific time span\.
   + **OS Process list** – Shows details for each process running in the selected instance\.
   + **Performance Insights** – Opens the Amazon RDS Performance Insights console for your DB instance\.

     
![\[RDS metrics viewing options\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/metrics0-aurora.png)
**Tip**  
To choose the time range of the metrics represented by the graphs, you can use the time range list\.  
To bring up a more detailed view, you can choose any graph\. You can also apply metric\-specific filters to the data\. 

### Viewing DB instance metrics with the CLI or API<a name="USER_Monitoring.DB"></a>

Amazon RDS integrates with CloudWatch metrics to provide a variety of DB instance metrics\. You can view CloudWatch metrics using the RDS console, AWS CLI, or API\.

 For a complete list of Amazon RDS metrics, go to [ Amazon RDS dimensions and metrics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/rds-metricscollected.html) in the *Amazon CloudWatch User Guide*\. 

#### Viewing DB metrics by using the CloudWatch CLI<a name="USER_Monitoring.CLI"></a>

**Note**  
The following CLI example requires the CloudWatch command line tools\. For more information on CloudWatch and to download the developer tools, see [Amazon CloudWatch](https://aws.amazon.com/cloudwatch) on the AWS website\. The `StartTime` and `EndTime` values supplied in this example are for illustration only\. Substitute appropriate start and end time values for your DB instance\.

**To view usage and performance statistics for a DB instance**
+ Use the CloudWatch command `mon-get-stats` with the following parameters\.

  ```
  1. PROMPT>mon-get-stats FreeStorageSpace --dimensions="DBInstanceIdentifier=mydbinstance" --statistics= Average 
  2.   --namespace="AWS/RDS" --start-time 2009-10-16T00:00:00 --end-time 2009-10-16T00:02:00
  ```

#### Viewing DB metrics by using the CloudWatch API<a name="USER_Monitoring.API"></a>

The `StartTime` and `EndTime` values supplied in this example are for illustration only\. Substitute appropriate start and end time values for your DB instance\.

**To view usage and performance statistics for a DB instance**
+ Call the CloudWatch API `GetMetricStatistics` with the following parameters:
  + `Statistics.member.1` = `Average`
  + `Namespace` = `AWS/RDS`
  + `StartTime` = `2009-10-16T00:00:00`
  + `EndTime` = `2009-10-16T00:02:00`
  + `Period` = `60`
  + `MeasureName` = `FreeStorageSpace`