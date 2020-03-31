# Using PostgreSQL Logical Replication with Aurora<a name="AuroraPostgreSQL.Replication.Logical"></a>

PostgreSQL logical replication provides fine\-grained control over replicating and synchronizing parts of a database\. For example, you can use logical replication to replicate an individual table of a database\. 

Following, you can find information about how to work with PostgreSQL logical replication and Amazon Aurora\. For more detailed information about the PostgreSQL implementation of logical replication, see [Logical Replication](https://www.postgresql.org/docs/current/logical-replication.html) and [Logical Decoding Concepts](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html) in the PostgreSQL documentation\.

**Note**  
Logical replication is available with Aurora PostgreSQL version 2\.2\.0 \(compatible with PostgreSQL 10\.6\) and later\.

Following, you can find information about how to work with PostgreSQL logical replication and Amazon Aurora\.

**Topics**
+ [Configuring Logical Replication](#AuroraPostgreSQL.Replication.Logical.Configure)
+ [Example of Logical Replication of a Database Table](#AuroraPostgreSQL.Replication.Logical.PostgreSQL-Example)
+ [Logical Replication Using the AWS Database Migration Service](#AuroraPostgreSQL.Replication.Logical.DMS-Example)

## Configuring Logical Replication<a name="AuroraPostgreSQL.Replication.Logical.Configure"></a>

To use logical replication, you first set the `rds.logical_replication` parameter for a cluster parameter group\. You then set up the publisher and subscriber\. 

Logical replication uses a publish and subscribe model\. *Publishers* and *subscribers* are the nodes\. A *publication* is a set of changes generated from one or more database tables\. You specify a publication on a publisher\. A *subscription* defines the connection to another database and one or more publications to which it subscribes\. You specify a subscription on a subscriber\. The publication and subscription make the connection between the publisher and subscriber\.

**Note**  
To perform logical replication for a PostgreSQL database, your AWS user account needs the `rds_superuser` role\.

**To enable PostgreSQL logical replication with Aurora**

1. Create a new DB cluster parameter group to use for logical replication, as described in [Creating a DB Cluster Parameter Group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.CreatingCluster)\. Use the following settings:
   + For **Parameter group family**, choose **aurora\-postgres10** or later\. 
   + For **Type**, choose **DB Cluster Parameter Group**\. 

1. Modify the cluster parameter group, as described in [Modifying Parameters in a DB Cluster Parameter Group](USER_WorkingWithParamGroups.md#USER_WorkingWithParamGroups.ModifyingCluster)\. Set the `rds.logical_replication` static parameter to 1\. 

   Enabling the `rds.logical_replication` parameter affects the DB cluster's performance\. 

**To configure a publisher for logical replication**

1. Set the publisher's cluster parameter group:
   + To use an existing Aurora PostgreSQL DB cluster for the publisher, the engine version must be 10\.6 or later\. Do the following:

     1. Modify the cluster parameter group to set it to the group that you created when you enabled logical replication\. For details about modifying an Aurora PostgreSQL DB cluster, see [Modifying an Amazon Aurora DB Cluster](Aurora.Modifying.md)\.

     1. Restart the DB cluster for static parameter changes to take effect\. The cluster parameter group includes a change to the static parameter `rds.logical_replication`\.
   + To use a new Aurora PostgreSQL DB cluster for the publisher, create the DB cluster using the following settings\. For details about creating an Aurora PostgreSQL DB cluster, see [Creating a DB Cluster](Aurora.CreateInstance.md#Aurora.CreateInstance.Creating)\.

     1. Choose the **Amazon Aurora** engine and choose the **PostgreSQL\-compatible** edition\.

     1. For **DB engine version**, choose an Aurora PostgreSQL engine that is compatible with PostgreSQL 10\.6 or greater\.

     1. For **DB cluster parameter group**, choose the group that you created when you enabled logical replication\.

1. Modify the inbound rules of the security group for the publisher to allow the subscriber to connect\. Usually, you do this by including the IP address of the subscriber in the security group\. For details about modifying a security group, see [Security Groups for Your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html) in the *Amazon Virtual Private Cloud User Guide*\. 

## Example of Logical Replication of a Database Table<a name="AuroraPostgreSQL.Replication.Logical.PostgreSQL-Example"></a>

To implement logical replication, use the PostgreSQL commands `CREATE PUBLICATION` and `CREATE SUBSCRIPTION`\. 

For this example, table data is replicated from an Aurora PostgreSQL database as the publisher to a PostgreSQL database as the subscriber\. Note that a subscriber database can be an RDS PostgreSQL database or an Aurora PostgreSQL database\. A subscriber can also be an application that uses PosgreSQL logical replication\. After the logical replication mechanism is set up, changes on the publisher are continually sent to the subscriber as they occur\. 

To set up logical replication for this example, do the following:

1. Configure an Aurora PostgreSQL DB cluster as the publisher\. To do so, create a new Aurora PostgreSQL DB cluster, as described when configuring the publisher in [Configuring Logical Replication](#AuroraPostgreSQL.Replication.Logical.Configure)\.

1. Set up the publisher database\. 

   For example, create a table using the following SQL statement on the publisher database\. 

   ```
   CREATE TABLE LogicalReplicationTest (a int PRIMARY KEY);
   ```

1. Insert data into the publisher database by using the following SQL statement\.

   ```
   INSERT INTO LogicalReplicationTest VALUES (generate_series(1,10000));
   ```

1. Create a publication on the publisher by using the following SQL statement\. 

   ```
   CREATE PUBLICATION testpub FOR TABLE LogicalReplicationTest;
   ```

1. Create your subscriber\. A subscriber database can be either of the following:
   + Aurora PostgreSQL database version 2\.2\.0 \(compatible with PostgreSQL 10\.6\) or later\. 
   + Amazon RDS for PostgreSQL database with the PostgreSQL DB engine version 10\.4 or later\.

   For this example, we create an Amazon RDS for PostgreSQL database as the subscriber\. For details on creating a DB instance, see [Creating a DB Instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_CreateDBInstance.html) in the *Amazon RDS User Guide\.* 

1. Set up the subscriber database\. 

   For this example, create a table like the one created for the publisher by using the following SQL statement\. 

   ```
   CREATE TABLE LogicalReplicationTest (a int PRIMARY KEY);
   ```

1. Verify that there is data in the table at the publisher but no data yet at the subscriber by using the following SQL statement on both databases\.

   ```
   SELECT count(*) FROM LogicalReplicationTest;
   ```

1. Create a subscription on the subscriber\. 

   Use the following SQL statement on the subscriber database and the following settings from the publisher cluster: 
   + **host** – The publisher cluster's writer DB instance\.
   + **port** – The port on which the writer DB instance is listening\. The default for PostgreSQL is 5432\.
   + **dbname** – The DB name of the publisher cluster\.

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

## Logical Replication Using the AWS Database Migration Service<a name="AuroraPostgreSQL.Replication.Logical.DMS-Example"></a>

You can use the AWS Database Migration Service \(AWS DMS\) to replicate a database or a portion of a database\. Use AWS DMS to migrate your data from an Aurora PostgreSQL database to another open source or commercial database\. For more information about AWS DMS, see the [AWS Database Migration Service User Guide](https://docs.aws.amazon.com/dms/latest/userguide/)\.

The following example shows how to set up logical replication from an Aurora PostgreSQL database as the publisher and then use AWS DMS for migration\. This example uses the same publisher and subscriber that were created in [Example of Logical Replication of a Database Table](#AuroraPostgreSQL.Replication.Logical.PostgreSQL-Example)\.

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

   To do this, PostgreSQL 10\.x and later databases require that you apply AWS DMS wrapper functions to the publisher database\. For details on this and later steps, see the instructions in [Using PostgreSQL Version 10\.x and Later as a Source for AWS DMS](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Source.PostgreSQL.html#CHAP_Source.PostgreSQL.v10) in the *AWS Database Migration Service User Guide\.*

1. Sign in to the AWS Management Console and open the AWS DMS console at [https://console\.aws\.amazon\.com/dms](https://console.aws.amazon.com/dms)\. At top right, choose the same AWS Region in which the publisher and subscriber are located\.

1. Create an AWS DMS replication instance\.

   Choose values that are the same as for your publisher's writer DB instance\. These include the following settings:
   + For **VPC**, choose the same VPC as for the writer DB instance\.
   + For **Replication Subnet Group**, choose the same subnet group as for the writer DB instance\.
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

   The rest of the task details depend on your migration project\. For more information about specifying all the details for DMS tasks, see [Working with AWS DMS Tasks](https://docs.aws.amazon.com/dms/latest/userguide/CHAP_Tasks.html) in the *AWS Database Migration Service User Guide\.*

After AWS DMS creates the task, it begins migrating data from the publisher to the subscriber\. 