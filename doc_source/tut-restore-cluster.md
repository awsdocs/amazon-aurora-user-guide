# Tutorial: Restore an Amazon Aurora DB cluster from a DB cluster snapshot<a name="tut-restore-cluster"></a>

A common scenario when working with Amazon Aurora is to have a DB instance that you work with occasionally but that you don't need full time\. For example, you might use a DB cluster to hold the data for a report that you run only quarterly\. One way to save money on such a scenario is to take a DB cluster snapshot of the DB cluster after the report is completed\. Then you delete the DB cluster, and restore it when you need to upload new data and run the report during the next quarter\.

When you restore a DB cluster, you provide the name of the DB cluster snapshot to restore from\. You then provide a name for the new DB cluster that's created from the restore operation\. For more detailed information on restoring DB clusters from snapshots, see [Restoring from a DB cluster snapshot](aurora-restore-snapshot.md)\.

In this tutorial, we also upgrade the restored DB cluster from Aurora MySQL version 2 \(compatible with MySQL 5\.7\) to Aurora MySQL version 3 \(compatible with MySQL 8\.0\)\.

## Restoring a DB cluster from a DB cluster snapshot using the Amazon RDS console<a name="tut-restore-cluster.console"></a>

When you restore a DB cluster from a snapshot using the AWS Management Console, the primary \(reader\) DB instance is also created\.

**To restore a DB cluster from a DB cluster snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Choose the DB cluster snapshot that you want to restore from\.

1. For **Actions**, choose **Restore snapshot**\.  
![\[Restore snapshot option in the Actions menu in the RDS console\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/tut-restore-cluster1.png)

   The **Restore snapshot** page appears\.

1. Under **DB instance settings**, do the following:

   1. Use the default setting for **DB engine**\.

   1. For **Available versions**, choose a MySQL–8\.0 compatible version, such as **Aurora MySQL 3\.02\.0 \(compatible with MySQL 8\.0\.23\)**\.  
![\[Restore snapshot page\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/tut-restore-cluster2.png)

1. Under **Settings**, for **DB cluster identifier** enter the unique name that you want to use for the restored DB cluster, for example **my\-80\-cluster**\.

1. Under **Connectivity**, use the default settings for the following:
   + **Virtual private cloud \(VPC\)**
   + **DB subnet group**
   + **Public access**
   + **VPC security group \(firewall\)**

1. Choose the **DB instance class**\.

   For this tutorial, choose **Burstable classes \(includes t classes\)**, and then choose **db\.t3\.medium**\.
**Note**  
We recommend using the T DB instance classes only for development and test servers, or other non\-production servers\. For more details on the T instance classes, see [DB instance class types](Concepts.DBInstanceClass.md#Concepts.DBInstanceClass.Types)\.  
![\[Instance configuration\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/tut-restore-cluster3.png)

1. For **Database authentication**, use the default setting\.

1. For **Encryption**, use the default settings\.

   If the source DB cluster for the snapshot was encrypted, the restored DB cluster is also encrypted\. You can't make it unencrypted\.

1. Expand **Additional configuration** at the bottom of the page\.  
![\[Additional configuration\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/tut-restore-cluster4.png)

1. Make the following choices:

   1. For this tutorial, use the default value for **DB cluster parameter group**\.

   1. For this tutorial, use the default value for **DB parameter group**\.

   1. For **Log exports**, select all of the check boxes\.

   1. For **Deletion protection**, select the **Enable deletion protection** check box\.

1. Choose **Restore DB instance**\.

The **Databases** page displays the restored DB cluster, with a status of `Creating`\.

![\[Restored DB cluster on the Databases page\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/tut-restore-cluster5.png)

While the primary DB instance is being created, it appears as a reader instance, but after creation it's a writer instance\.

## Restoring a DB cluster from a DB cluster snapshot using the AWS CLI<a name="tut-restore-cluster.CLI"></a>

Restoring a DB cluster from a snapshot using the AWS CLI has two steps:

1. Restore the DB cluster using the [restore\-db\-cluster\-from\-snapshot](https://docs.aws.amazon.com/cli/latest/reference/rds/restore-db-cluster-from-snapshot.html) command\.

1. Create the primary \(writer\) DB instance using the [create\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-instance.html) command\.

### Restoring the DB cluster<a name="tut-restore-cluster.CLI.restore"></a>

You use the `restore-db-cluster-from-snapshot` command\. The following options are required:
+ `--db-cluster-identifier` – The name of the restored DB cluster\.
+ `--snapshot-identifier` – The name of the DB snapshot to restore from\.
+ `--engine` – The database engine of the restored DB cluster\. It must be compatible with the database engine of the source DB cluster\.

  The choices are the following:
  + `aurora` – Aurora MySQL 5\.6 compatible\.
  + `aurora-mysql` – Aurora MySQL 5\.7 and 8\.0 compatible\.
  + `aurora-postgresql` – Aurora PostgreSQL compatible\.

  In this example, we use `aurora-mysql`\.
+ `--engine-version` – The version of the restored DB cluster\. In this example, we use a MySQL\-8\.0 compatible version\.

The following example restores an Aurora MySQL 8\.0–compatible DB cluster named `my-new-80-cluster` from a DB cluster snapshot named `my-57-cluster-snapshot`\.

**To restore the DB cluster**
+ Use one of the following commands\.

  For Linux, macOS, or Unix:

  ```
  aws rds restore-db-cluster-from-snapshot \
      --db-cluster-identifier my-new-80-cluster \
      --snapshot-identifier my-57-cluster-snapshot \
      --engine aurora-mysql \
      --engine-version 8.0.mysql_aurora.3.02.0
  ```

  For Windows:

  ```
  aws rds restore-db-cluster-from-snapshot ^
      --db-cluster-identifier my-new-80-cluster ^
      --snapshot-identifier my-57-cluster-snapshot ^
      --engine aurora-mysql ^
      --engine-version 8.0.mysql_aurora.3.02.0
  ```

The output resembles the following\.

```
{
    "DBCluster": {
        "AllocatedStorage": 1,
        "AvailabilityZones": [
            "eu-central-1b",
            "eu-central-1c",
            "eu-central-1a"
        ],
        "BackupRetentionPeriod": 14,
        "DatabaseName": "",
        "DBClusterIdentifier": "my-new-80-cluster",
        "DBClusterParameterGroup": "default.aurora-mysql8.0",
        "DBSubnetGroup": "default",
        "Status": "creating",
        "Endpoint": "my-new-80-cluster.cluster-############.eu-central-1.rds.amazonaws.com",
        "ReaderEndpoint": "my-new-80-cluster.cluster-ro-############.eu-central-1.rds.amazonaws.com",
        "MultiAZ": false,
        "Engine": "aurora-mysql",
        "EngineVersion": "8.0.mysql_aurora.3.02.0",
        "Port": 3306,
        "MasterUsername": "admin",
        "PreferredBackupWindow": "01:55-02:25",
        "PreferredMaintenanceWindow": "thu:21:14-thu:21:44",
        "ReadReplicaIdentifiers": [],
        "DBClusterMembers": [],
        "VpcSecurityGroups": [
            {
                "VpcSecurityGroupId": "sg-########",
                "Status": "active"
            }
        ],
        "HostedZoneId": "Z1RLNU0EXAMPLE",
        "StorageEncrypted": true,
        "KmsKeyId": "arn:aws:kms:eu-central-1:123456789012:key/#######-5ccc-49cc-8aaa-############",
        "DbClusterResourceId": "cluster-ZZ12345678ITSJUSTANEXAMPLE",
        "DBClusterArn": "arn:aws:rds:eu-central-1:123456789012:cluster:my-new-80-cluster",
        "AssociatedRoles": [],
        "IAMDatabaseAuthenticationEnabled": false,
        "ClusterCreateTime": "2022-07-05T20:45:42.171000+00:00",
        "EngineMode": "provisioned",
        "DeletionProtection": false,
        "HttpEndpointEnabled": false,
        "CopyTagsToSnapshot": false,
        "CrossAccountClone": false,
        "DomainMemberships": [],
        "TagList": []
    }
}
```

### Creating the primary \(writer\) DB instance<a name="tut-restore-cluster.CLI.create"></a>

To create the primary \(writer\) DB instance, you use the `create-db-instance` command\. The following options are required:
+ `--db-cluster-identifier` – The name of the restored DB cluster\.
+ `--db-instance-identifier` – The name of the primary DB instance\.
+ `--db-instance-class` – The instance class of the primary DB instance\. In this example, we use `db.t3.medium`\.
**Note**  
We recommend using the T DB instance classes only for development and test servers, or other non\-production servers\. For more details on the T instance classes, see [DB instance class types](Concepts.DBInstanceClass.md#Concepts.DBInstanceClass.Types)\.
+ `--engine` – The database engine of the primary DB instance\. It must be the same database engine as the restored DB cluster uses\.

  The choices are the following:
  + `aurora` – Aurora MySQL 5\.6 compatible\.
  + `aurora-mysql` – Aurora MySQL 5\.7 and 8\.0 compatible\.
  + `aurora-postgresql` – Aurora PostgreSQL compatible\.

  In this example, we use `aurora-mysql`\.

The following example creates a primary \(writer\) DB instance named `my-new-80-cluster-instance` in the restored Aurora MySQL 8\.0–compatible DB cluster named `my-new-80-cluster`\.

**To create the primary DB instance**
+ Use one of the following commands\.

  For Linux, macOS, or Unix:

  ```
  aws rds create-db-instance \
      --db-cluster-identifier my-new-80-cluster \
      --db-instance-identifier my-new-80-cluster-instance \
      --db-instance-class db.t3.medium \
      --engine aurora-mysql
  ```

  For Windows:

  ```
  aws rds create-db-instance ^
      --db-cluster-identifier my-new-80-cluster ^
      --db-instance-identifier my-new-80-cluster-instance ^
      --db-instance-class db.t3.medium ^
      --engine aurora-mysql
  ```

The output resembles the following\.

```
{
    "DBInstance": {
        "DBInstanceIdentifier": "my-new-80-cluster-instance",
        "DBInstanceClass": "db.t3.medium",
        "Engine": "aurora-mysql",
        "DBInstanceStatus": "creating",
        "MasterUsername": "admin",
        "AllocatedStorage": 1,
        "PreferredBackupWindow": "01:55-02:25",
        "BackupRetentionPeriod": 14,
        "DBSecurityGroups": [],
        "VpcSecurityGroups": [
            {
                "VpcSecurityGroupId": "sg-########",
                "Status": "active"
            }
        ],
        "DBParameterGroups": [
            {
                "DBParameterGroupName": "default.aurora-mysql8.0",
                "ParameterApplyStatus": "in-sync"
            }
        ],
        "DBSubnetGroup": {
            "DBSubnetGroupName": "default",
            "DBSubnetGroupDescription": "default",
            "VpcId": "vpc-2305ca49",
            "SubnetGroupStatus": "Complete",
            "Subnets": [
                {
                    "SubnetIdentifier": "subnet-########",
                    "SubnetAvailabilityZone": {
                        "Name": "eu-central-1a"
                    },
                    "SubnetOutpost": {},
                    "SubnetStatus": "Active"
                },
                {
                    "SubnetIdentifier": "subnet-########",
                    "SubnetAvailabilityZone": {
                        "Name": "eu-central-1b"
                    },
                    "SubnetOutpost": {},
                    "SubnetStatus": "Active"
                },
                {
                    "SubnetIdentifier": "subnet-########",
                    "SubnetAvailabilityZone": {
                        "Name": "eu-central-1c"
                    },
                    "SubnetOutpost": {},
                    "SubnetStatus": "Active"
                }
            ]
        },
        "PreferredMaintenanceWindow": "sat:02:41-sat:03:11",
        "PendingModifiedValues": {},
        "MultiAZ": false,
        "EngineVersion": "8.0.mysql_aurora.3.02.0",
        "AutoMinorVersionUpgrade": true,
        "ReadReplicaDBInstanceIdentifiers": [],
        "LicenseModel": "general-public-license",
        "OptionGroupMemberships": [
            {
                "OptionGroupName": "default:aurora-mysql-8-0",
                "Status": "in-sync"
            }
        ],
        "PubliclyAccessible": false,
        "StorageType": "aurora",
        "DbInstancePort": 0,
        "DBClusterIdentifier": "my-new-80-cluster",
        "StorageEncrypted": true,
        "KmsKeyId": "arn:aws:kms:eu-central-1:534026745191:key/#######-5ccc-49cc-8aaa-############",
        "DbiResourceId": "db-5C6UT5PU0YETANOTHEREXAMPLE",
        "CACertificateIdentifier": "rds-ca-2019",
        "DomainMemberships": [],
        "CopyTagsToSnapshot": false,
        "MonitoringInterval": 0,
        "PromotionTier": 1,
        "DBInstanceArn": "arn:aws:rds:eu-central-1:123456789012:db:my-new-80-cluster-instance",
        "IAMDatabaseAuthenticationEnabled": false,
        "PerformanceInsightsEnabled": false,
        "DeletionProtection": false,
        "AssociatedRoles": [],
        "TagList": []
    }
}
```