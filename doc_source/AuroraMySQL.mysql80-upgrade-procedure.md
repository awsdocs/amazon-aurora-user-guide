# Upgrading to Aurora MySQL version 3<a name="AuroraMySQL.mysql80-upgrade-procedure"></a>

For specific upgrade paths to upgrade your database from Aurora MySQL version 2 to version 3, you can use one of the following approaches:
+ Perform an in\-place upgrade\. Follow the procedure in [How to perform an in\-place upgrade](AuroraMySQL.Updates.MajorVersionUpgrade.md#AuroraMySQL.Upgrading.Procedure)\.
+ Create a snapshot of the version 2 cluster and restore the snapshot to create a new version 3 cluster\. Follow the procedure in [Restoring from a DB cluster snapshot](aurora-restore-snapshot.md)\.

**Tip**  
 In some cases, you might specify the option to upload database logs to CloudWatch when you upgrade\. If so, examine the logs in CloudWatch to diagnose any issues that occur during the upgrade operation\. The CLI examples in this section demonstrate how to do so using the `--enable-cloudwatch-logs-exports` option\. 

**Topics**
+ [Upgrade planning for Aurora MySQL version 3](#AuroraMySQL.mysql80-planning)
+ [Example of upgrading from Aurora MySQL version 2 to version 3 using the snapshot restore method](#AuroraMySQL.mysql80-upgrade-example-v2-v3)
+ [Troubleshooting upgrade issues with Aurora MySQL version 3](#AuroraMySQL.mysql80-upgrade-troubleshooting)
+ [Post\-upgrade cleanup for Aurora MySQL version 3](#AuroraMySQL.mysql80-post-upgrade)

## Upgrade planning for Aurora MySQL version 3<a name="AuroraMySQL.mysql80-planning"></a>

 To help you decide the right time and approach to upgrade, you can learn the differences between Aurora MySQL version 3 and your current Aurora and MySQL environment: 
+  If you are converting from RDS for MySQL 8\.0 or MySQL 8\.0 Community Edition, see [Comparison of Aurora MySQL version 3 and MySQL 8\.0 Community Edition](Aurora.AuroraMySQL.Compare-80-v3.md)\. 
+  If you are upgrading from Aurora MySQL version 2, RDS for MySQL 5\.7, or community MySQL 5\.7, see [Comparison of Aurora MySQL version 2 and Aurora MySQL version 3](Aurora.AuroraMySQL.Compare-v2-v3.md)\. 
+  Create new MySQL 8\.0\-compatible versions of any custom parameter groups\. Apply any necessary custom parameter values to the new parameter groups\. Consult [Parameter changes for Aurora MySQL version 3](Aurora.AuroraMySQL.Compare-v2-v3.md#AuroraMySQL.mysql80-parameter-changes) to learn about parameter changes\. 
**Note**  
For most parameter settings, you can choose the custom parameter group at two points\. These are when you create the cluster or associate the parameter group with the cluster later\.  
However, if you use a nondefault setting for the `lower_case_table_names` parameter, you must set up the custom parameter group with this setting in advance\. Then specify the parameter group when you perform the snapshot restore to create the cluster\. Any change to the `lower_case_table_names` parameter has no effect after the cluster is created\.  
We recommend that you use the same setting for `lower_case_table_names` when you upgrade from Aurora MySQL version 2 to version 3\.  
With an Aurora global database based on Aurora MySQL, you can't perform an in\-place upgrade from Aurora MySQL version 2 to version 3 if the `lower_case_table_names` parameter is turned on\. For more information on the methods that you can use, see [Major version upgrades](aurora-global-database-upgrade.md#aurora-global-database-upgrade.major)\.
+ Review your Aurora MySQL version 2 database schema and object definitions for the usage of new reserved keywords introduced in MySQL 8\.0 Community Edition\. Do so before you upgrade\. For more information, see [MySQL 8\.0 New Keywords and Reserved Words](https://dev.mysql.com/doc/mysqld-version-reference/en/keywords-8-0.html#keywords-new-in-8-0) in the MySQL documentation\.

 You can also find more MySQL\-specific upgrade considerations and tips in [Changes in MySQL 8\.0](https://dev.mysql.com/doc/refman/8.0/en/upgrading-from-previous-series.html) in the *MySQL Reference Manual*\. For example, you can use the command `mysqlcheck --check-upgrade` to analyze your existing Aurora MySQL databases and identify potential upgrade issues\. 

**Note**  
We recommend using larger DB instance classes when upgrading to Aurora MySQL version 3 using the in\-place upgrade or snapshot restore technique\. Examples are db\.r5\.24xlarge and db\.r6g\.16xlarge\. This helps the upgrade process to complete faster by using the majority of available CPU capacity on the DB instance\. You can change to the DB instance class that you want after the major version upgrade is complete\.

 After you finish the upgrade itself, you can follow the post\-upgrade procedures in [Post\-upgrade cleanup for Aurora MySQL version 3](#AuroraMySQL.mysql80-post-upgrade)\. Finally, test your application's functionality and performance\. 

 If you are converting from RDS from MySQL or community MySQL, follow the migration procedure explained in [Migrating data to an Amazon Aurora MySQL DB cluster](AuroraMySQL.Migrating.md)\. In some cases, you might use binary log replication to synchronize your data with an Aurora MySQL version 3 cluster as part of the migration\. If so, the source system must run a version that's compatible with MySQL 5\.7, or a MySQL 8\.0–compatible version that is 8\.0\.23 or lower\. 

## Example of upgrading from Aurora MySQL version 2 to version 3 using the snapshot restore method<a name="AuroraMySQL.mysql80-upgrade-example-v2-v3"></a>

 The following AWS CLI example demonstrates the steps to upgrade an Aurora MySQL version 2 cluster to Aurora MySQL version 3\. 

 The first step is to determine the version of the cluster that you want to upgrade\. The following AWS CLI command shows how\. The output confirms that the original cluster is a MySQL 5\.7–compatible one that's running Aurora MySQL version 2\.09\.2\. 

 This cluster has at least one DB instance\. For the upgrade process to work properly, this original cluster requires a writer instance\. 

```
$ aws rds describe-db-clusters --db-cluster-id cluster-57-upgrade-ok \
  --query '*[].EngineVersion' --output text

5.7.mysql_aurora.2.09.2
```

 The following command shows how to check which upgrade paths are available from a specific version\. In this case, the original cluster is running version `5.7.mysql_aurora.2.09.2`\. The output shows that this version can be upgraded to Aurora MySQL version 3\. 

 If the original cluster uses a version number that is too low to upgrade to Aurora MySQL version 3, the upgrade requires an additional step\. First, restore the snapshot to create a new cluster that could be upgraded to Aurora MySQL version 3\. Then, take a snapshot of that intermediate cluster\. Finally, restore the snapshot of the intermediate cluster to create a new Aurora MySQL version 3 cluster\. 

```
$ aws rds describe-db-engine-versions --engine aurora-mysql \
  --engine-version 5.7.mysql_aurora.2.09.2 \
  --query 'DBEngineVersions[].ValidUpgradeTarget[].EngineVersion'
[
    "5.7.mysql_aurora.2.09.3",
    "5.7.mysql_aurora.2.10.0",
    "5.7.mysql_aurora.2.10.1",
    "5.7.mysql_aurora.2.10.2",
    "8.0.mysql_aurora.3.01.1",
    "8.0.mysql_aurora.3.02.0"
]
```

 The following command creates a snapshot of the cluster to upgrade to Aurora MySQL version 3\. The original cluster remains intact\. You later create a new Aurora MySQL version 3 cluster from the snapshot\. 

```
aws rds create-db-cluster-snapshot --db-cluster-id cluster-57-upgrade-ok \
  --db-cluster-snapshot-id cluster-57-upgrade-ok-snapshot
{
  "DBClusterSnapshotIdentifier": "cluster-57-upgrade-ok-snapshot",
  "DBClusterIdentifier": "cluster-57-upgrade-ok",
  "SnapshotCreateTime": "2021-10-06T23:20:18.087000+00:00"
}
```

 The following command restores the snapshot to a new cluster that's running Aurora MySQL version 3\. 

```
$ aws rds restore-db-cluster-from-snapshot \
  --snapshot-id cluster-57-upgrade-ok-snapshot \
  --db-cluster-id cluster-80-restored --engine aurora-mysql \
  --engine-version 8.0.mysql_aurora.3.02.0 \
  --enable-cloudwatch-logs-exports '["error","general","slowquery","audit"]'
{
  "DBClusterIdentifier": "cluster-80-restored",
  "Engine": "aurora-mysql",
  "EngineVersion": "8.0.mysql_aurora.3.02.0",
  "Status": "creating"
}
```

 Restoring the snapshot sets up the storage for the cluster and establishes the database version that the cluster can use\. The compute capacity of the cluster is separate from the storage\. Thus, you set up any DB instances for the cluster after the cluster itself is created\. The following example creates a writer DB instance using one of the db\.r5 instance classes\. 

**Tip**  
 You might have administration scripts that create DB instances using older instance classes such as db\.r3, db\.r4, db\.t2, or db\.t3\. If so, modify your scripts to use one of the instance classes that are supported with Aurora MySQL version 3\. For information about the instance classes that you can use with Aurora MySQL version 3, see [Instance class support](Aurora.AuroraMySQL.Compare-v2-v3.md#AuroraMySQL.mysql80-instance-classes)\. 

```
$ aws rds create-db-instance --db-instance-identifier instance-running-version-3 \
  --db-cluster-identifier cluster-80-restored \
  --db-instance-class db.r5.xlarge --engine aurora-mysql
{
  "DBInstanceIdentifier": "instance-running-version-3",
  "DBClusterIdentifier": "cluster-80-restored",
  "DBInstanceClass": "db.r5.xlarge",
  "EngineVersion": "8.0.mysql_aurora.3.02.0",
  "DBInstanceStatus": "creating"
}
```

 After the upgrade is finished, you can verify the Aurora\-specific version number of the cluster by using the AWS CLI\. 

```
$ aws rds describe-db-clusters --db-cluster-id cluster-80-restored \
  --query '*[].EngineVersion' --output text
8.0.mysql_aurora.3.02.0
```

 You can also verify the MySQL\-specific version of the database engine by calling the `version` function\. 

```
mysql> select version();
+-----------+
| version() |
+-----------+
| 8.0.23    |
+-----------+
```

## Troubleshooting upgrade issues with Aurora MySQL version 3<a name="AuroraMySQL.mysql80-upgrade-troubleshooting"></a>

 If your upgrade to Aurora MySQL version 3 doesn't complete successfully, you can diagnose the cause of the problem\. Then you can make any required changes to the original database schema or table data and run the upgrade process again\. 

 If the upgrade process to Aurora MySQL version 3 fails, the problem is detected while creating and then upgrading the writer instance for the restored snapshot\. Aurora leaves behind the original 5\.7\-compatible writer instance\. That way, you can examine the log from the preliminary checks that Aurora runs before performing the upgrade\. The following examples start with a 5\.7\-compatible database that requires some changes before it can be upgraded to Aurora MySQL version 3\. The examples demonstrate how the first attempted upgrade doesn't succeed and how to examine the log file\. They also show how to fix the problems and run a successful upgrade\. 

 First, we create a new MySQL 5\.7\-compatible cluster named `problematic-57-80-upgrade`\. As the name suggests, this cluster contains at least one schema object that causes a problem during an upgrade to a MySQL 8\.0\-compatible version\. 

```
$ aws rds create-db-cluster --engine aurora-mysql \
  --engine-version 5.7.mysql_aurora.2.10.0 \
  --db-cluster-identifier problematic-57-80-upgrade \
  --master-username my_username \
  --master-user-password my_password
{
  "DBClusterIdentifier": "problematic-57-80-upgrade",
  "Status": "creating"
}

$ aws rds create-db-instance \
  --db-instance-identifier instance-preupgrade \
  --db-cluster-identifier problematic-57-80-upgrade \
  --db-instance-class db.t2.small --engine aurora-mysql
{
  "DBInstanceIdentifier": "instance-preupgrade",
  "DBClusterIdentifier": "problematic-57-80-upgrade",
  "DBInstanceClass": "db.t2.small",
  "DBInstanceStatus": "creating"
}

$ aws rds wait db-instance-available \
  --db-instance-identifier instance-preupgrade
```

 In the cluster that we intend to upgrade, we introduce a problematic table\. Creating a `FULLTEXT` index and then dropping the index leaves behind some metadata that causes a problem during the upgrade\. 

```
$ mysql -u my_username -p \
  -h problematic-57-80-upgrade.cluster-example123.us-east-1.rds.amazonaws.com

mysql> create database problematic_upgrade;
Query OK, 1 row affected (0.02 sec)

mysql> use problematic_upgrade;
Database changed
mysql> CREATE TABLE dangling_fulltext_index
    ->   (id INT AUTO_INCREMENT PRIMARY KEY, txtcol TEXT NOT NULL)
    ->   ENGINE=InnoDB;
Query OK, 0 rows affected (0.05 sec)

mysql> ALTER TABLE dangling_fulltext_index ADD FULLTEXT(txtcol);
Query OK, 0 rows affected, 1 warning (0.14 sec)

mysql> ALTER TABLE dangling_fulltext_index DROP INDEX txtcol;
Query OK, 0 rows affected (0.06 sec)
```

 This example attempts to perform the upgrade procedure\. We take a snapshot of the original cluster and wait for snapshot creation to complete\. Then we restore the snapshot, specifying the MySQL 8\.0\-compatible version number\. We also create the writer instance for the cluster\. That is the point where the upgrade processing actually happens\. Then we wait for the writer instance to become available\. That's the point where the upgrade process is finished, whether it succeeded or failed\. 

**Tip**  
 If you restore the snapshot using the AWS Management Console, Aurora creates the writer instance automatically and upgrades it to the requested engine version\. 

```
$ aws rds create-db-cluster-snapshot --db-cluster-id problematic-57-80-upgrade \
  --db-cluster-snapshot-id problematic-57-80-upgrade-snapshot
{
  "DBClusterSnapshotIdentifier": "problematic-57-80-upgrade-snapshot",
  "DBClusterIdentifier": "problematic-57-80-upgrade",
  "Engine": "aurora-mysql",
  "EngineVersion": "5.7.mysql_aurora.2.10.0"
}

$ aws rds wait db-cluster-snapshot-available \
  --db-cluster-snapshot-id problematic-57-80-upgrade-snapshot

$ aws rds restore-db-cluster-from-snapshot \
  --snapshot-id problematic-57-80-upgrade-snapshot \
  --db-cluster-id cluster-80-attempt-1 --engine aurora-mysql \
  --engine-version 8.0.mysql_aurora.3.02.0 \
  --enable-cloudwatch-logs-exports '["error","general","slowquery","audit"]'
{
  "DBClusterIdentifier": "cluster-80-attempt-1",
  "Engine": "aurora-mysql",
  "EngineVersion": "8.0.mysql_aurora.3.02.0",
  "Status": "creating"
}

$ aws rds create-db-instance --db-instance-identifier instance-attempt-1 \
  --db-cluster-identifier cluster-80-attempt-1 \
  --db-instance-class db.r5.xlarge --engine aurora-mysql
{
  "DBInstanceIdentifier": "instance-attempt-1",
  "DBClusterIdentifier": "cluster-80-attempt-1",
  "DBInstanceClass": "db.r5.xlarge",
  "EngineVersion": "8.0.mysql_aurora.3.02.0",
  "DBInstanceStatus": "creating"
}

$ aws rds wait db-instance-available \
  --db-instance-identifier instance-attempt-1
```

 Now we examine the newly created cluster and associated instance to verify if the upgrade succeeded\. The cluster and instance are still running a MySQL 5\.7\-compatible version\. That means that the upgrade failed\. When an upgrade fails, Aurora only leaves the writer instance behind so that you can examine any log files\. You can't restart the upgrade process with that newly created cluster\. After you correct the problem by making changes in your original cluster, you must run the upgrade steps again: make a new snapshot of the original cluster and restore it to another MySQL 8\.0\-compatible cluster\. 

```
$ aws rds describe-db-clusters \
  --db-cluster-identifier cluster-80-attempt-1 \
  --query '*[].[Status]' --output text
available
$ aws rds describe-db-clusters \
  --db-cluster-identifier cluster-80-attempt-1 \
  --query '*[].[EngineVersion]' --output text
5.7.mysql_aurora.2.10.0

$ aws rds describe-db-instances \
  --db-instance-identifier instance-attempt-1 \
  --query '*[].{DBInstanceStatus:DBInstanceStatus}' --output text
available
$ aws rds describe-db-instances \
  --db-instance-identifier instance-attempt-1 \
  --query '*[].[EngineVersion]' --output text
5.7.mysql_aurora.2.10.0
```

 To get a summary of what happened during the upgrade process, we get a listing of events for the newly created writer instance\. In this example, we list the events over the last 600 minutes to cover the whole time interval of the upgrade process\. The events in the listing aren't necessarily in chronological order\. The highlighted event shows the problem that confirms the cluster couldn't be upgraded\. 

```
$ aws rds describe-events \
  --source-identifier instance-attempt-1 --source-type db-instance \
  --duration 600
{
    "Events": [
        {
            "SourceIdentifier": "instance-attempt-1",
            "SourceType": "db-instance",
            "Message": "Binlog position from crash recovery is mysql-bin-changelog.000001 154",
            "EventCategories": [],
            "Date": "2021-12-03T20:26:17.862000+00:00",
            "SourceArn": "arn:aws:rds:us-east-1:123456789012:db:instance-attempt-1"
        },
        {
            "SourceIdentifier": "instance-attempt-1",
            "SourceType": "db-instance",
            "Message": "Database cluster is in a state that cannot be upgraded:
PreUpgrade checks failed: PreChecker Found 1 errors",
            "EventCategories": [
                "maintenance"
            ],
            "Date": "2021-12-03T20:26:50.436000+00:00",
            "SourceArn": "arn:aws:rds:us-east-1:123456789012:db:instance-attempt-1"
        },
        {
            "SourceIdentifier": "instance-attempt-1",
            "SourceType": "db-instance",
            "Message": "DB instance created",
            "EventCategories": [
                "creation"
            ],
            "Date": "2021-12-03T20:26:58.830000+00:00",
            "SourceArn": "arn:aws:rds:us-east-1:123456789012:db:instance-attempt-1"
        },
...
```

 To diagnose the exact cause of the problem, examine the database logs for the newly created writer instance\. When an upgrade to an 8\.0\-compatible version fails, the instance contains a log file with the file name `upgrade-prechecks.log`\. This example shows how to detect the presence of that log and then download it to a local file for examination\. 

```
$ aws rds describe-db-log-files --db-instance-identifier instance-attempt-1 \
  --query '*[].[LogFileName]' --output text
error/mysql-error-running.log
error/mysql-error-running.log.2021-12-03.20
error/mysql-error-running.log.2021-12-03.21
error/mysql-error.log
external/mysql-external.log
upgrade-prechecks.log

$ aws rds download-db-log-file-portion --db-instance-identifier instance-attempt-1 \
  --log-file-name upgrade-prechecks.log --starting-token 0 \
  --output text >upgrade_prechecks.log
```

 The `upgrade-prechecks.log` file is in JSON format\. We download it using the `--output text` option to avoid encoding JSON output within another JSON wrapper\. For Aurora MySQL version 3 upgrades, this log always includes certain informational and warning messages\. It only includes error messages if the upgrade fails\. If the upgrade succeeds, the log file isn't produced at all\. The following excerpts show the kinds of entries you can expect to find\. 

```
$ cat upgrade-prechecks.log
{
    "serverAddress": "/tmp%2Fmysql.sock",
    "serverVersion": "5.7.12",
    "targetVersion": "8.0.23",
    "auroraServerVersion": "2.10.0",
    "auroraTargetVersion": "3.02.0",
    "outfilePath": "/rdsdbdata/tmp/PreChecker.log",
    "checksPerformed": [
```

 If `"detectedProblems"` is empty, the upgrade didn't encounter any occurrences of that type of problem\. You can ignore those entries\. 

```
{
    "id": "oldTemporalCheck",
    "title": "Usage of old temporal type",
    "status": "OK",
   "detectedProblems": []
},
```

 Checks for removed variables or changed default values aren't performed automatically\. Aurora uses the parameter group mechanism instead of a physical configuration file\. You always receive some messages with this `CONFIGURATION_ERROR` status, whether or not the variable changes have any effect on your database\. You can consult the MySQL documentation for details about these changes\. 

```
{
    "id": "removedSysLogVars",
    "title": "Removed system variables for error logging to the system log configuration",
    "status": "CONFIGURATION_ERROR",
    "description": "To run this check requires full path to MySQL server
configuration file to be specified at 'configPath' key of options dictionary",
    "documentationLink": "https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-13.html#mysqld-8-0-13-logging"
},
```

 This warning about obsolete date and time data types occurs if the `SQL_MODE` setting in your parameter group is left at the default value\. Your database might or might not contain columns with the affected types\. 

```
{
    "id": "zeroDatesCheck",
    "title": "Zero Date, Datetime, and Timestamp values",
    "status": "OK",
    "description": "Warning: By default zero date/datetime/timestamp
values are no longer allowed in MySQL, as of 5.7.8 NO_ZERO_IN_DATE and
NO_ZERO_DATE are included in SQL_MODE by default. These modes should be used
with strict mode as they will be merged with strict mode in a future release.
If you do not include these modes in your SQL_MODE setting, you are able to
insert date/datetime/timestamp values that contain zeros. It is strongly
advised to replace zero values with valid ones, as they may not work
correctly in the future.",
    "documentationLink": "https://lefred.be/content/mysql-8-0-and-wrong-dates/",
    "detectedProblems": [
        {
            "level": "Warning",
            "dbObject": "global.sql_mode",
            "description": "does not contain either NO_ZERO_DATE or
NO_ZERO_IN_DATE which allows insertion of zero dates"
        }
    ]
},
```

 When the `detectedProblems` field contains entries with a `level` value of `Error`, that means that the upgrade can't succeed until you correct those issues\. 

```
{
    "id": "getDanglingFulltextIndex",
    "title": "Tables with dangling FULLTEXT index reference",
    "status": "OK",
    "description": "Error: The following tables contain dangling
FULLTEXT index which is not supported. It is recommended to rebuild the
table before upgrade.",
    "detectedProblems": [
        {
           "level": "Error",
           "dbObject": "problematic_upgrade.dangling_fulltext_index",
           "description": "Table `problematic_upgrade.dangling_fulltext_index` contains dangling FULLTEXT index. Kindly recreate the table before upgrade."
        }
    ]
},
```

**Tip**  
 To summarize all of those errors and display the associated object and description fields, you can run the command `grep -A 2 '"level": "Error"'` on the contents of the `upgrade-prechecks.log` file\. Doing so displays each error line and the two lines after it\. These contain the name of the corresponding database object and guidance about how to correct the problem\.   

```
$ cat upgrade-prechecks.log | grep -A 2 '"level": "Error"'
"level": "Error",
"dbObject": "problematic_upgrade.dangling_fulltext_index",
"description": "Table `problematic_upgrade.dangling_fulltext_index` contains dangling FULLTEXT index. Kindly recreate the table before upgrade."
```

 This `defaultAuthenticationPlugin` check always displays this warning message for Aurora MySQL version 3 upgrades\. That's because Aurora MySQL version 3 uses the `mysql_native_password` plugin instead of `caching_sha2_password`\. You don't need to take any action for this warning\. 

```
{
    "id": "defaultAuthenticationPlugin",
    "title": "New default authentication plugin considerations",
    "description": "Warning: The new default authentication plugin
'caching_sha2_password' offers more secure password hashing than previously
used 'mysql_native_password' (and consequent improved client connection
...
    "documentationLink": "https://dev.mysql.com/doc/refman/8.0/en/upgrading-from-previous-series.html#upgrade-caching-sha2-password-compatibility-issues\nhttps://dev.mysql.com/doc/refman/8.0/en/upgrading-from-previous-series.html#upgrade-caching-sha2-password-replication"
}
```

 The end of the `upgrade-prechecks.log` file summarizes how many checks encountered each type of minor or severe problem\. A nonzero `errorCount` indicates that the upgrade failed\. 

```
    ],
    "errorCount": 1,
    "warningCount": 2,
    "noticeCount": 0,
    "Summary": "1 errors were found. Please correct these issues before upgrading to avoid compatibility issues."
}
```

 The next sequence of examples demonstrates how to fix this particular issue and run the upgrade process again\. This time, the upgrade succeeds\. 

First, we go back to the original cluster\. Then we run `OPTIMIZE TABLE tbl_name [, tbl_name] ...` on the tables causing the following error:

Table `tbl\_name` contains dangling FULLTEXT index\. Kindly recreate the table before upgrade\.

```
$ mysql -u my_username -p \
  -h problematic-57-80-upgrade.cluster-example123.us-east-1.rds.amazonaws.com
mysql> show databases;
+---------------------+
| Database            |
+---------------------+
| information_schema  |
| mysql               |
| performance_schema  |
| problematic_upgrade |
| sys                 |
+---------------------+
5 rows in set (0.00 sec)
mysql> use problematic_upgrade;
mysql> show tables;
+-------------------------------+
| Tables_in_problematic_upgrade |
+-------------------------------+
| dangling_fulltext_index       |
+-------------------------------+
1 row in set (0.00 sec)
mysql> OPTIMIZE TABLE dangling_fulltext_index;
Query OK, 0 rows affected (0.05 sec)
```

For more information, see [Optimizing InnoDB Full\-Text Indexes](https://dev.mysql.com/doc/refman/8.0/en/fulltext-fine-tuning.html#fulltext-optimize) and [OPTIMIZE TABLE Statement](https://dev.mysql.com/doc/refman/8.0/en/optimize-table.html) in the MySQL documentation\.

As an alternative solution you could create a new table with the same structure and contents as the one with faulty metadata\. In practice, you would probably rename this table back to the original table name after the upgrade\.

```
$ mysql -u my_username -p \
  -h problematic-57-80-upgrade.cluster-example123.us-east-1.rds.amazonaws.com

mysql> show databases;
+---------------------+
| Database            |
+---------------------+
| information_schema  |
| mysql               |
| performance_schema  |
| problematic_upgrade |
| sys                 |
+---------------------+
5 rows in set (0.00 sec)

mysql> use problematic_upgrade;
mysql> show tables;
+-------------------------------+
| Tables_in_problematic_upgrade |
+-------------------------------+
| dangling_fulltext_index       |
+-------------------------------+
1 row in set (0.00 sec)

mysql> desc dangling_fulltext_index;
+--------+---------+------+-----+---------+----------------+
| Field  | Type    | Null | Key | Default | Extra          |
+--------+---------+------+-----+---------+----------------+
| id     | int(11) | NO   | PRI | NULL    | auto_increment |
| txtcol | text    | NO   |     | NULL    |                |
+--------+---------+------+-----+---------+----------------+
2 rows in set (0.00 sec)

mysql> CREATE TABLE recreated_table LIKE dangling_fulltext_index;
Query OK, 0 rows affected (0.03 sec)

mysql> INSERT INTO recreated_table SELECT * FROM dangling_fulltext_index;
Query OK, 0 rows affected (0.00 sec)

mysql> drop table dangling_fulltext_index;
Query OK, 0 rows affected (0.05 sec)
```

 Now we go through the same process as before\. We create a snapshot from the original cluster and restore the snapshot to a new MySQL 8\.0\-compatible cluster\. Then we create a writer instance to complete the upgrade process\. 

```
$ aws rds create-db-cluster-snapshot --db-cluster-id problematic-57-80-upgrade \
  --db-cluster-snapshot-id problematic-57-80-upgrade-snapshot-2
{
  "DBClusterSnapshotIdentifier": "problematic-57-80-upgrade-snapshot-2",
  "DBClusterIdentifier": "problematic-57-80-upgrade",
  "Engine": "aurora-mysql",
  "EngineVersion": "5.7.mysql_aurora.2.10.0"
}

$ aws rds wait db-cluster-snapshot-available \
  --db-cluster-snapshot-id problematic-57-80-upgrade-snapshot-2

$ aws rds restore-db-cluster-from-snapshot \
  --snapshot-id problematic-57-80-upgrade-snapshot-2 \
  --db-cluster-id cluster-80-attempt-2 --engine aurora-mysql \
  --engine-version 8.0.mysql_aurora.3.02.0 \
  --enable-cloudwatch-logs-exports '["error","general","slowquery","audit"]'
{
  "DBClusterIdentifier": "cluster-80-attempt-2",
  "Engine": "aurora-mysql",
  "EngineVersion": "8.0.mysql_aurora.3.02.0",
  "Status": "creating"
}

$ aws rds create-db-instance --db-instance-identifier instance-attempt-2 \
  --db-cluster-identifier cluster-80-attempt-2 \
  --db-instance-class db.r5.xlarge --engine aurora-mysql
{
  "DBInstanceIdentifier": "instance-attempt-2",
  "DBClusterIdentifier": "cluster-80-attempt-2",
  "DBInstanceClass": "db.r5.xlarge",
  "EngineVersion": "8.0.mysql_aurora.3.02.0",
  "DBInstanceStatus": "creating"
}

$ aws rds wait db-instance-available \
  --db-instance-identifier instance-attempt-2
```

 This time, checking the version after the upgrade completes confirms that the version number changed to reflect Aurora MySQL version 3\. We can connect to the database and confirm the MySQL engine version is an 8\.0\-compatible one\. We confirm that the upgraded cluster contains the fixed version of the table that caused the original upgrade problem\. 

```
$ aws rds describe-db-clusters \
  --db-cluster-identifier cluster-80-attempt-2 \
  --query '*[].[EngineVersion]' --output text
8.0.mysql_aurora.3.02.0
$ aws rds describe-db-instances \
  --db-instance-identifier instance-attempt-2 \
  --query '*[].[EngineVersion]' --output text
8.0.mysql_aurora.3.02.0

$ mysql -h cluster-80-attempt-2.cluster-example123.us-east-1.rds.amazonaws.com \
  -u my_username -p

mysql> select version();
+-----------+
| version() |
+-----------+
| 8.0.23    |
+-----------+
1 row in set (0.00 sec)

mysql> show databases;
+---------------------+
| Database            |
+---------------------+
| information_schema  |
| mysql               |
| performance_schema  |
| problematic_upgrade |
| sys                 |
+---------------------+
5 rows in set (0.00 sec)

mysql> use problematic_upgrade;
Database changed
mysql> show tables;
+-------------------------------+
| Tables_in_problematic_upgrade |
+-------------------------------+
| recreated_table               |
+-------------------------------+
1 row in set (0.00 sec)
```

## Post\-upgrade cleanup for Aurora MySQL version 3<a name="AuroraMySQL.mysql80-post-upgrade"></a>

After you finish upgrading any Aurora MySQL version 2 clusters to Aurora MySQL version 3, you can perform these other cleanup actions:
+  Create new MySQL 8\.0–compatible versions of any custom parameter groups\. Apply any necessary custom parameter values to the new parameter groups\. 
+  Update any CloudWatch alarms, setup scripts, and so on to use the new names for any metrics whose names were affected by inclusive language changes\. For a list of such metrics, see [Inclusive language changes for Aurora MySQL version 3](Aurora.AuroraMySQL.Compare-v2-v3.md#AuroraMySQL.8.0-inclusive-language)\. 
+  Update any AWS CloudFormation templates to use the new names for any configuration parameters whose names were affected by inclusive language changes\. For a list of such parameters, see [Inclusive language changes for Aurora MySQL version 3](Aurora.AuroraMySQL.Compare-v2-v3.md#AuroraMySQL.8.0-inclusive-language)\. 

### Spatial indexes<a name="AuroraMySQL.mysql80-spatial"></a>

 After upgrading to Aurora MySQL version 3, check if you need to drop or recreate objects and indexes related to spatial indexes\. Before MySQL 8\.0, Aurora could optimize spatial queries using indexes that didn't contain a spatial resource identifier \(SRID\)\. Aurora MySQL version 3 only uses spatial indexes containing SRIDs\. During an upgrade, Aurora automatically drops any spatial indexes without SRIDs and prints warning messages in the database log\. If you observe such warning messages, create new spatial indexes with SRIDs after the upgrade\. For more information about changes to spatial functions and data types in MySQL 8\.0, see [Changes in MySQL 8\.0](https://dev.mysql.com/doc/refman/8.0/en/upgrading-from-previous-series.html) in the *MySQL Reference Manual*\. 