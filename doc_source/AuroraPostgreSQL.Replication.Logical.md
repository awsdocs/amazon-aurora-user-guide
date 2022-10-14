# Using PostgreSQL logical replication with Aurora<a name="AuroraPostgreSQL.Replication.Logical"></a>

By using PostgreSQL's logical replication feature with your Aurora PostgreSQL DB cluster, you can replicate and synchronize individual tables rather than the entire database instance\. Logical replication uses a publish and subscribe model to replicate changes from a source to one or more recipients\. It works by using change records from the PostgreSQL write\-ahead log \(WAL\)\. The source, or *publisher*, sends WAL data for the specified tables to one or more recipients \(*subscriber*\), thus replicating the changes and keeping a subscriber's table synchronized with the publisher's table\. The set of changes from the publisher are identified using a *publication*\. Subscribers get the changes by creating a *subscription* that defines the connection to the publisher's database and its publications\. A *replication slot* is the mechanism used in this scheme to track progress of a subscription\. 

For Aurora PostgreSQL DB clusters, the WAL records are saved on Aurora storage\. The Aurora PostgreSQL DB cluster that's acting as the publisher in a logical replication scenario reads the WAL data from Aurora storage, decodes it, and sends it to the subscriber so that the changes can be applied to the table on that instance\. The publisher uses a *logical decoder* to decode the data for use by subscribers\. By default, Aurora PostgreSQL DB clusters use the native PostgreSQL `pgoutput` plugin when sending data\. Other logical decoders are available\. For example, Aurora PostgreSQL also supports the `[wal2json](https://github.com/eulerto/wal2json)` plugin that converts WAL data to JSON\. 

Logical replication is supported by all currently available Aurora PostgreSQL versions\. For more information, [Amazon Aurora PostgreSQL updates](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html) in the *Release Notes for Aurora PostgreSQL*\. 

For more information about PostgreSQL logical replication, see [Logical replication](https://www.postgresql.org/docs/current/logical-replication.html) and [Logical decoding concepts](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html) in the PostgreSQL documentation\.

In the following topics\. you can find information about set up logical replication between your Aurora PostgreSQL DB clusters\. 

**Topics**
+ [Setting up logical replication for your Aurora PostgreSQL DB cluster](#AuroraPostgreSQL.Replication.Logical.Configure)
+ [Turning off logical replication](#AuroraPostgreSQL.Replication.Logical.Stop)
+ [Managing logical slots for Aurora PostgreSQL](#AuroraPostgreSQL.Replication.Logical.Configure.managing-logical-slots)
+ [Example: Using logical replication with Aurora PostgreSQL DB clusters](#AuroraPostgreSQL.Replication.Logical.PostgreSQL-Example)
+ [Example: Logical replication using Aurora PostgreSQL and AWS Database Migration Service](#AuroraPostgreSQL.Replication.Logical.DMS-Example)

## Setting up logical replication for your Aurora PostgreSQL DB cluster<a name="AuroraPostgreSQL.Replication.Logical.Configure"></a>

Setting up logical replication requires `rds_superuser` privileges\. Your Aurora PostgreSQL DB cluster must be configured to use a custom DB cluster parameter group so that you can set the necessary parameters as detailed in the procedure following\. For more information, see [Working with DB cluster parameter groups](USER_WorkingWithDBClusterParamGroups.md)\. 

**To set up PostgreSQL logical replication for an Aurora PostgreSQL DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose your Aurora PostgreSQL DB cluster\.

1. Open the **Configuration** tab\. Among the Instance details, find the **Parameter group** link\. 

1. Choose the link to open the custom parameters associated with your Aurora PostgreSQL DB cluster\. 

1. In the **Parameters** search field, type `rds` to find the `rds.logical_replication` parameter\. The default value for this parameter is `0`, meaning that it's turned off by default\. 

1. Choose **Edit parameters** to access the property values, and then choose `1` from the selector to turn on the feature\. Depending on your expected usage, you might also need to change the settings for the following parameters\. However, in many cases, the default values are sufficient\. 
   + `max_replication_slots` – Set this parameter to a value that's at least equal to your planned total number of logical replication publications and subscriptions\. If you are using AWS DMS, this parameter should equal at least your planned change data capture tasks from the cluster, plus logical replication publications and subscriptions\. 
   + `max_wal_senders` and `max_logical_replication_workers` – Set these parameters to a value that's at least equal to the number of logical replication slots that you intend to be active, or the number of active AWS DMS tasks for change data capture\. Leaving a logical replication slot inactive prevents the vacuum from removing obsolete tuples from tables, so we recommend that you monitor replication slots and remove inactive slots as needed\. 
   + `max_worker_processes` – Set this parameter to a value that's at least equal to the total of the `max_logical_replication_workers`, `autovacuum_max_workers`, and `max_parallel_workers` values\. On small DB instance classes, background worker processes can affect application workloads, so monitor the performance of your database if you set `max_worker_processes` higher than the default value\. \(The default value is the result of `GREATEST(${DBInstanceVCPU*2},8}`, which means that, by default, this is either 8 or twice the CPU equivalent of the DB instance class, whichever is greater\)\.

1. Choose **Save changes**\.

1. Reboot the writer instance of your Aurora PostgreSQL DB cluster so that your changes takes effect\. In the Amazon RDS console, choose the primary DB instance of the cluster and choose **Reboot** from the **Actions** menu\. 

1. When the instance is available, you can verify that logical replication is turned on, as follows\. 

   1. Use `psql` to connect to the writer instance of your Aurora PostgreSQL DB cluster\.

      ```
      psql --host=your-db-cluster-instance-1.aws-region.rds.amazonaws.com --port=5432 --username=postgrespostgres --password --dbname=labdb
      ```

   1. Verify that logical replication has been enabled by using the following command\.

      ```
      labdb=> SHOW rds.logical_replication;
       rds.logical_replication
      -------------------------
       on
      (1 row)
      ```

   1. Verify that the `wal_level` is set to `logical`\. 

      ```
      labdb=> SHOW wal_level;
        wal_level
      -----------
       logical
      (1 row)
      ```

For an example of using logical replication to keep a database table synchronized with changes from a source Aurora PostgreSQL DB cluster, see [Example: Using logical replication with Aurora PostgreSQL DB clusters](#AuroraPostgreSQL.Replication.Logical.PostgreSQL-Example)\. 

## Turning off logical replication<a name="AuroraPostgreSQL.Replication.Logical.Stop"></a>

After completing your replication tasks, you should stop the replication process, drop replication slots, and turn off logical replication\. Before dropping slots, make sure that they're no longer needed\. Active replication slots can't be dropped\. 

**To turn off logical replication**

1. Drop all replication slots\.

   To drop all of the replication slots, connect to the publisher and run the following SQL command\.

   ```
   SELECT pg_drop_replication_slot(slot_name) FROM pg_replication_slots 
      WHERE slot_name IN (SELECT slot_name FROM pg_replication_slots);
   ```

   The replication slots can't be active when you run this command\.

1. Modify the custom DB cluster parameter group associated with the publisher as detailed in [Setting up logical replication for your Aurora PostgreSQL DB cluster](#AuroraPostgreSQL.Replication.Logical.Configure), but set the `rds.logical_replication` parameter to 0\. 

   For more information about custom parameter groups, see [Modifying parameters in a DB cluster parameter group](USER_WorkingWithDBClusterParamGroups.md#USER_WorkingWithParamGroups.ModifyingCluster)\. 

1. Restart the publisher Aurora PostgreSQL DB cluster for the change to the `rds.logical_replication` parameter to take effect\.

## Managing logical slots for Aurora PostgreSQL<a name="AuroraPostgreSQL.Replication.Logical.Configure.managing-logical-slots"></a>

Streaming activity is captured in the `pg_replication_origin_status` view\. To see the contents of this view, you can use the `pg_show_replication_origin_status()` function, as shown following:

```
SELECT * FROM pg_show_replication_origin_status();
local_id | external_id | remote_lsn | local_lsn
----------+-------------+------------+-----------
(0 rows)
```

You can get a list of your logical slots by using the following SQL query\.

```
SELECT * FROM pg_replication_slots;
```

To drop a logical slot, use the following command\.

```
SELECT pg_drop_replication_slot('test_slot');
pg_drop_replication_slot
–––––––––
(1 row)
```

## Example: Using logical replication with Aurora PostgreSQL DB clusters<a name="AuroraPostgreSQL.Replication.Logical.PostgreSQL-Example"></a>

The following procedure shows you how to start logical replication between two Aurora PostgreSQL DB clusters\. Both the publisher and the subscriber must be configured for logical replication as detailed in [Setting up logical replication for your Aurora PostgreSQL DB cluster](#AuroraPostgreSQL.Replication.Logical.Configure)\.

The Aurora PostgreSQL DB cluster that's the designated publisher must also allow access to the replication slot\. To do so, modify the security group associated with the Aurora PostgreSQL DB cluster 's virtual public cloud \(VPC\) based on the Amazon VPC service\. Allow inbound access by adding the security group associated with the subscriber's VPC to the publisher's security group\. For more information, see [Control traffic to resources using security groups](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) in the *Amazon VPC User Guide*\. 

With these prelimnary steps complete, you can use PostgreSQL commands `CREATE PUBLICATION` on the publisher and the `CREATE SUBSCRIPTION` on the subscriber, as detailed in the following procedure\. 

**To start the logical replication process between two Aurora PostgreSQL DB clusters**

These steps assume that your Aurora PostgreSQL DB clusters have a writer instance with a database in which to create the example tables\.

1. **On the publisher Aurora PostgreSQL DB cluster**

   1. Create a table using the following SQL statement\.

      ```
      CREATE TABLE LogicalReplicationTest (a int PRIMARY KEY);
      ```

   1. Insert data into the publisher database by using the following SQL statement\.

      ```
      INSERT INTO LogicalReplicationTest VALUES (generate_series(1,10000));
      ```

   1. Verify that data exists in the table by using the folling SQL statement\.

      ```
      SELECT count(*) FROM LogicalReplicationTest;
      ```

   1. Create a publication for this table by using the `CREATE PUBLICATION` statement, as follows\.

      ```
      CREATE PUBLICATION testpub FOR TABLE LogicalReplicationTest;
      ```

1. **On the subscriber Aurora PostgreSQL DB cluster**

   1. Create the same `LogicalReplicationTest` table on the subscriber that you created on the publisher, as follows\.

      ```
      CREATE TABLE LogicalReplicationTest (a int PRIMARY KEY);
      ```

   1. Verify that this table is empty\.

      ```
      SELECT count(*) FROM LogicalReplicationTest;
      ```

   1. Create a subscription to get the changes from the publisher\. You need to use the following details about the publisher Aurora PostgreSQL DB cluster\.
      + **host** – The publisher Aurora PostgreSQL DB cluster's writer DB instance\.
      + **port** – The port on which the writer DB instance is listening\. The default for PostgreSQL is 5432\.
      + **dbname** – The name of the database\.

      ```
      CREATE SUBSCRIPTION testsub CONNECTION 
         'host=publisher-cluster-writer-endpoint port=5432 dbname=db-name user=user password=password' 
         PUBLICATION testpub;
      ```

      After the subscription is created, a logical replication slot is created at the publisher\.

   1. To verify for this example that the initial data is replicated on the subscriber, use the following SQL statement on the subscriber database\.

      ```
      SELECT count(*) FROM LogicalReplicationTest;
      ```

Any further changes on the publisher are replicated to the subscriber\.

Logical replication affects performance\. We recommend that you turn off logical replication after your replication tasks are complete\. 

## Example: Logical replication using Aurora PostgreSQL and AWS Database Migration Service<a name="AuroraPostgreSQL.Replication.Logical.DMS-Example"></a>

You can use the AWS Database Migration Service \(AWS DMS\) to replicate a database or a portion of a database\. Use AWS DMS to migrate your data from an Aurora PostgreSQL database to another open source or commercial database\. For more information about AWS DMS, see the [AWS Database Migration Service User Guide](https://docs.aws.amazon.com/dms/latest/userguide/)\.

The following example shows how to set up logical replication from an Aurora PostgreSQL database as the publisher and then use AWS DMS for migration\. This example uses the same publisher and subscriber that were created in [Example: Using logical replication with Aurora PostgreSQL DB clusters](#AuroraPostgreSQL.Replication.Logical.PostgreSQL-Example)\.

To set up logical replication with AWS DMS, you need details about your publisher and subscriber from Amazon RDS\. In particular, you need details about the publisher's writer DB instance and the subscriber's DB instance\.

Get the following information for the publisher's writer DB instance:
+ The virtual private cloud \(VPC\) identifier
+ The subnet group
+ The Availability Zone \(AZ\)
+ The VPC security group
+ The DB instance ID

Get the following information for the subscriber's DB instance:
+ The DB instance ID
+ The source engine

**To use AWS DMS for logical replication with Aurora PostgreSQL**

1. Prepare the publisher database to work with AWS DMS\. 

   To do this, PostgreSQL 10\.x and later databases require that you apply AWS DMS wrapper functions to the publisher database\. For details on this and later steps, see the instructions in [Using PostgreSQL version 10\.x and later as a source for AWS DMS](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Source.PostgreSQL.html#CHAP_Source.PostgreSQL.v10) in the *AWS Database Migration Service User Guide\.*

1. Sign in to the AWS Management Console and open the AWS DMS console at [https://console.aws.amazon.com/dms/v2](https://console.aws.amazon.com/dms/v2)\. At top right, choose the same AWS Region in which the publisher and subscriber are located\.

1. Create an AWS DMS replication instance\.

   Choose values that are the same as for your publisher's writer DB instance\. These include the following settings:
   + For **VPC**, choose the same VPC as for the writer DB instance\.
   + For **Replication Subnet Group**, choose a subnet group with the same values as the writer DB instance\. Create a new one if necessary\.
   + For **Availability zone**, choose the same zone as for the writer DB instance\.
   + For **VPC Security Group**, choose the same group as for the writer DB instance\.

1. Create an AWS DMS endpoint for the source\. 

   Specify the publisher as the source endpoint by using the following settings: 
   + For **Endpoint type**, choose **Source endpoint**\. 
   + Choose **Select RDS DB Instance**\.
   + For **RDS Instance**, choose the DB identifier of the publisher's writer DB instance\.
   + For **Source engine**, choose **postgres**\.

1. Create an AWS DMS endpoint for the target\. 

   Specify the subscriber as the target endpoint by using the following settings:
   + For **Endpoint type**, choose **Target endpoint**\. 
   + Choose **Select RDS DB Instance**\.
   + For **RDS Instance**, choose the DB identifier of the subscriber DB instance\.
   + Choose a value for **Source engine**\. For example, if the subscriber is an RDS PostgreSQL database, choose **postgres**\. If the subscriber is an Aurora PostgreSQL database, choose **aurora\-postgresql**\.

1. Create an AWS DMS database migration task\. 

   You use a database migration task to specify what database tables to migrate, to map data using the target schema, and to create new tables on the target database\. At a minimum, use the following settings for **Task configuration**:
   + For **Replication instance**, choose the replication instance that you created in an earlier step\.
   + For **Source database endpoint**, choose the publisher source that you created in an earlier step\.
   + For **Target database endpoint**, choose the subscriber target that you created in an earlier step\.

   The rest of the task details depend on your migration project\. For more information about specifying all the details for DMS tasks, see [Working with AWS DMS tasks](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Tasks.html) in the *AWS Database Migration Service User Guide\.*

After AWS DMS creates the task, it begins migrating data from the publisher to the subscriber\. 