# Rebooting an Amazon Aurora DB cluster or Amazon Aurora DB instance<a name="USER_RebootCluster"></a><a name="reboot"></a>

 You might need to reboot your DB cluster or some instances within the cluster, usually for maintenance reasons\. For example, suppose that you modify the parameters within a parameter group or associate a different parameter group with your cluster\. In these cases, you must reboot the cluster for the changes to take effect\. Similarly, you might reboot one or more reader DB instances within the cluster\. You can arrange the reboot operations for individual instances to minimize downtime for the entire cluster\. 

 The time required to reboot each DB instance in your cluster depends on the database activity at the time of reboot\. It also depends on the recovery process of your specific DB engine\. If it's practical, reduce database activity on that particular instance before starting the reboot process\. Doing so can reduce the time needed to restart the database\. 

 You can only reboot each DB instance in your cluster when it's in the available state\. A DB instance can be unavailable for several reasons\. These include the cluster being stopped state, a modification being applied to the instance, and a maintenance\-window action such as a version upgrade\. 

 Rebooting a DB instance restarts the database engine process\. Rebooting a DB instance results in a momentary outage, during which the DB instance status is set to *rebooting*\. 

**Note**  
 If a DB instance isn't using the latest changes to its associated DB parameter group, the AWS Management Console shows the DB parameter group with a status of **pending\-reboot**\. The **pending\-reboot** parameter groups status doesn't result in an automatic reboot during the next maintenance window\. To apply the latest parameter changes to that DB instance, manually reboot the DB instance\. For more information about parameter groups, see [Working with parameter groups](USER_WorkingWithParamGroups.md)\. 

**Topics**
+ [Rebooting a DB instance within an Aurora cluster](#aurora-reboot-db-instance)
+ [Rebooting an Aurora cluster \(Aurora PostgreSQL and Aurora MySQL before version 2\.10\)](#aurora-reboot-cluster)
+ [Rebooting an Aurora MySQL cluster \(version 2\.10 and higher\)](#aurora-mysql-survivable-replicas)
+ [Checking uptime for Aurora clusters and instances](#USER_Reboot.Uptime)
+ [Examples of Aurora reboot operations](#USER_Reboot.Examples)

## Rebooting a DB instance within an Aurora cluster<a name="aurora-reboot-db-instance"></a>

 This procedure is the most important operation that you take when performing reboots with Aurora\. Many of the maintenance procedures involve rebooting one or more Aurora DB instances in a particular order\. 

### Console<a name="USER_RebootInstance.Console"></a>

**To reboot a DB instance**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1.  In the navigation pane, choose **Databases**, and then choose the DB instance that you want to reboot\. 

1.  For **Actions**, choose **Reboot**\. 

    The **Reboot DB Instance** page appears\. 

1.  Choose **Reboot** to reboot your DB instance\. 

    Or choose **Cancel**\. 

### AWS CLI<a name="USER_RebootInstance.CLI"></a>

 To reboot a DB instance by using the AWS CLI, call the [https://docs.aws.amazon.com/cli/latest/reference/rds/reboot-db-instance.html](https://docs.aws.amazon.com/cli/latest/reference/rds/reboot-db-instance.html) command\. 

**Example**  
For Linux, macOS, or Unix:  

```
aws rds reboot-db-instance \
    --db-instance-identifier mydbinstance
```
For Windows:  

```
aws rds reboot-db-instance ^
    --db-instance-identifier mydbinstance
```

### RDS API<a name="USER_RebootInstance.API"></a>

 To reboot a DB instance by using the Amazon RDS API, call the [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RebootDBInstance.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RebootDBInstance.html) operation\. 

## Rebooting an Aurora cluster \(Aurora PostgreSQL and Aurora MySQL before version 2\.10\)<a name="aurora-reboot-cluster"></a>

 In Aurora PostgreSQL\-Compatible Edition, in Aurora MySQL\-Compatible Edition version 1, and in Aurora MySQL before version 2\.10, you reboot an entire Aurora DB cluster by rebooting the writer DB instance of that cluster\. To do so, follow the procedure in [Rebooting a DB instance within an Aurora cluster](#aurora-reboot-db-instance)\. 

 Rebooting the writer DB instance also initiates a reboot for each reader DB instance in the cluster\. That way, any cluster\-wide parameter changes are applied to all DB instances at the same time\. However, the reboot of all DB instances causes a brief outage for the cluster\. The reader DB instances remain unavailable until the writer DB instance finishes rebooting and becomes available\. 

 In the RDS console, the writer DB instance has the value **Writer** under the **Role** column on the **Databases** page\. In the RDS CLI, the output of the `describe-db-clusters` command includes a section `DBClusterMembers`\. The `DBClusterMembers` element representing the writer DB instance has a value of `true` for the `IsClusterWriter` field\. 

**Important**  
 In Aurora MySQL 2\.10 and higher, the reboot behavior is different: the reader DB instances typically remain available while you reboot the writer instance\. Then you can reboot the reader instances at a convenient time\. You can reboot the reader instances on a staggered schedule if you want some reader instances to always be available\. For more information, see [Rebooting an Aurora MySQL cluster \(version 2\.10 and higher\)](#aurora-mysql-survivable-replicas)\. 

## Rebooting an Aurora MySQL cluster \(version 2\.10 and higher\)<a name="aurora-mysql-survivable-replicas"></a><a name="survivable_replicas"></a>

 In Aurora MySQL version 2\.10 and higher, you can reboot the writer instance of your Aurora MySQL cluster without rebooting the reader instances in the cluster\. Doing so can help maintain high availability of the cluster for read operations while you reboot the writer instance\. You can reboot the reader instances later, on a schedule that's convenient for you\. For example, for a production cluster, you might reboot the reader instances one at a time, starting only after the reboot of the primary instance is finished\. For each DB instance that you reboot, follow the procedure in [Rebooting a DB instance within an Aurora cluster](#aurora-reboot-db-instance)\. 

 Before Aurora MySQL 2\.10, rebooting the primary instance caused a reboot for each reader instance at the same time\. If your Aurora MySQL cluster is running an older version, use the reboot procedure in [Rebooting an Aurora cluster \(Aurora PostgreSQL and Aurora MySQL before version 2\.10\)](#aurora-reboot-cluster) instead\. 

**Important**  
 The change to reboot behavior in Aurora MySQL 2\.10 and higher is different for Aurora global databases\. If you reboot the writer instance for the primary cluster in an Aurora global database, the reader instances in the primary cluster remain available\. However, the DB instances in any secondary clusters reboot at the same time\. 

 You frequently reboot the cluster after making changes to cluster parameter groups\. You make parameter changes by following the procedures in [Working with parameter groups](USER_WorkingWithParamGroups.md)\. Suppose that you reboot the writer DB instance in an Aurora MySQL cluster to apply changes to cluster parameters\. Some or all of the reader DB instances might continue using the old parameter settings\. However, the different parameter settings don't affect the data integrity of the cluster\. Any cluster parameters that affect the organization of data files are only used by the writer DB instance\. For example, you can update cluster parameters such as `binlog_format` and `innodb_purge_threads` on the writer instance before the reader instances\. Only the writer instance is writing binary logs and purging undo records\. 

 For parameters that change how queries interpret SQL statements or query output, you might need to take care to reboot the reader instances immediately\. You do this to avoid unexpected application behavior during queries\. For example, suppose that you change the `lower_case_table_names` parameter and reboot the writer instance\. In this case, the reader instances might not be able to access a newly created table until they are all rebooted\. 

 For a list of all the Aurora MySQL cluster parameters, see [Cluster\-level parameters](AuroraMySQL.Reference.ParameterGroups.md#AuroraMySQL.Reference.Parameters.Cluster)\. 

**Tip**  
 Aurora MySQL might still reboot some of the reader instances along with the writer instance if your cluster is processing a workload with high throughput\.   
 The reduction in the number of reboots applies during failover operations also\. Aurora MySQL only restarts the writer DB instance and the failover target during a failover\. Other reader DB instances in the cluster remain available to continue processing queries through connections to the reader endpoint\. Thus, you can improve availability during a failover by having more than one reader DB instance in a cluster\. 

## Checking uptime for Aurora clusters and instances<a name="USER_Reboot.Uptime"></a>

 You can check and monitor the length of time since the last reboot for each DB instance in your Aurora cluster\. The Amazon CloudWatch metric `EngineUptime` reports the number of seconds since the last time a DB instance was started\. You can examine this metric at a point in time to find out the uptime for the DB instance\. You can also monitor this metric over time to detect when the instance is rebooted\. 

 You can also examine the `EngineUptime` metric at the cluster level\. The `Minimum` and `Maximum` dimensions report the smallest and largest uptime values for all DB instances in the cluster\. To check the most recent time when any reader instance in a cluster was rebooted, or restarted for another reason, monitor the cluster\-level metric using the `Minimum` dimension\. To check which instance in the cluster has gone the longest without a reboot, monitor the cluster\-level metric using the `Maximum` dimension\. For example, you might want to confirm that all DB instances in the cluster were rebooted after a configuration change\. 

**Tip**  
 For long\-term monitoring, we recommend monitoring the `EngineUptime` metric for individual instances instead of at the cluster level\. The cluster\-level `EngineUptime` metric is set to zero when a new DB instance is added to the cluster\. Such cluster changes can happen as part of maintenance and scaling operations such as those performed by Auto Scaling\. 

 The following CLI examples show how to examine the `EngineUptime` metric for the writer and reader instances in a cluster\. The examples use a cluster named `tpch100g`\. This cluster has a writer DB instance `instance-1234`\. It also has two reader DB instances, `instance-7448` and `instance-6305`\. 

 First, the `reboot-db-instance` command reboots one of the reader instances\. The `wait` command waits until the instance is finished rebooting\. 

```
$ aws rds reboot-db-instance --db-instance-identifier instance-6305
{
    "DBInstance": {
        "DBInstanceIdentifier": "instance-6305",
        "DBInstanceStatus": "rebooting",
...
$ aws rds wait db-instance-available --db-instance-id instance-6305
```

 The CloudWatch `get-metric-statistics` command examines the `EngineUptime` metric over the last five minutes at one\-minute intervals\. The uptime for the `instance-6305` instance is reset to zero and begins counting upwards again\. This AWS CLI example for Linux uses `$()` variable substitution to insert the appropriate timestamps into the CLI commands\. It also uses the Linux `sort` command to order the output by the time the metric was collected\. That timestamp value is the third field in each line of output\. 

```
$ aws cloudwatch get-metric-statistics --metric-name "EngineUptime" \
  --start-time "$(date -d '5 minutes ago')" --end-time "$(date -d 'now')" \
  --period 60 --namespace "AWS/RDS" --statistics Maximum \
  --dimensions Name=DBInstanceIdentifier,Value=instance-6305 --output text \
  | sort -k 3
EngineUptime
DATAPOINTS	231.0	2021-03-16T18:19:00+00:00	Seconds
DATAPOINTS	291.0	2021-03-16T18:20:00+00:00	Seconds
DATAPOINTS	351.0	2021-03-16T18:21:00+00:00	Seconds
DATAPOINTS	411.0	2021-03-16T18:22:00+00:00	Seconds
DATAPOINTS	471.0	2021-03-16T18:23:00+00:00	Seconds
```

 The minimum uptime for the cluster is reset to zero because one of the instances in the cluster was rebooted\. The maximum uptime for the cluster isn't reset because at least one of the DB instances in the cluster remained available\. 

```
$ aws cloudwatch get-metric-statistics --metric-name "EngineUptime" \
  --start-time "$(date -d '5 minutes ago')" --end-time "$(date -d 'now')" \
  --period 60 --namespace "AWS/RDS" --statistics Minimum \
  --dimensions Name=DBClusterIdentifier,Value=tpch100g --output text \
  | sort -k 3
EngineUptime
DATAPOINTS	63099.0	2021-03-16T18:12:00+00:00	Seconds
DATAPOINTS	63159.0	2021-03-16T18:13:00+00:00	Seconds
DATAPOINTS	63219.0	2021-03-16T18:14:00+00:00	Seconds
DATAPOINTS	63279.0	2021-03-16T18:15:00+00:00	Seconds
DATAPOINTS	51.0	2021-03-16T18:16:00+00:00	Seconds

$ aws cloudwatch get-metric-statistics --metric-name "EngineUptime" \
  --start-time "$(date -d '5 minutes ago')" --end-time "$(date -d 'now')" \
  --period 60 --namespace "AWS/RDS" --statistics Maximum \
  --dimensions Name=DBClusterIdentifier,Value=tpch100g --output text \
  | sort -k 3
EngineUptime
DATAPOINTS	63389.0	2021-03-16T18:16:00+00:00	Seconds
DATAPOINTS	63449.0	2021-03-16T18:17:00+00:00	Seconds
DATAPOINTS	63509.0	2021-03-16T18:18:00+00:00	Seconds
DATAPOINTS	63569.0	2021-03-16T18:19:00+00:00	Seconds
DATAPOINTS	63629.0	2021-03-16T18:20:00+00:00	Seconds
```

 Then another `reboot-db-instance` command reboots the writer instance of the cluster\. Another `wait` command pauses until the writer instance is finished rebooting\. 

```
$ aws rds reboot-db-instance --db-instance-identifier instance-1234
{
  "DBInstanceIdentifier": "instance-1234",
  "DBInstanceStatus": "rebooting",
...
$ aws rds wait db-instance-available --db-instance-id instance-1234
```

 Now the `EngineUptime` metric for the writer instance shows that the instance `instance-1234` was rebooted recently\. The reader instance `instance-6305` was also rebooted automatically along with the writer instance\. This cluster is running Aurora MySQL 2\.09, which doesn't keep the reader instances running as the writer instance reboots\. 

```
$ aws cloudwatch get-metric-statistics --metric-name "EngineUptime" \
  --start-time "$(date -d '5 minutes ago')" --end-time "$(date -d 'now')" \
  --period 60 --namespace "AWS/RDS" --statistics Maximum \
  --dimensions Name=DBInstanceIdentifier,Value=instance-1234 --output text \
  | sort -k 3
EngineUptime
DATAPOINTS	63749.0	2021-03-16T18:22:00+00:00	Seconds
DATAPOINTS	63809.0	2021-03-16T18:23:00+00:00	Seconds
DATAPOINTS	63869.0	2021-03-16T18:24:00+00:00	Seconds
DATAPOINTS	41.0	2021-03-16T18:25:00+00:00	Seconds
DATAPOINTS	101.0	2021-03-16T18:26:00+00:00	Seconds

$ aws cloudwatch get-metric-statistics --metric-name "EngineUptime" \
  --start-time "$(date -d '5 minutes ago')" --end-time "$(date -d 'now')" \
  --period 60 --namespace "AWS/RDS" --statistics Maximum \
  --dimensions Name=DBInstanceIdentifier,Value=instance-6305 --output text \
  | sort -k 3
EngineUptime
DATAPOINTS	411.0	2021-03-16T18:22:00+00:00	Seconds
DATAPOINTS	471.0	2021-03-16T18:23:00+00:00	Seconds
DATAPOINTS	531.0	2021-03-16T18:24:00+00:00	Seconds
DATAPOINTS	49.0	2021-03-16T18:26:00+00:00	Seconds
```

## Examples of Aurora reboot operations<a name="USER_Reboot.Examples"></a>

 The following Aurora MySQL examples show different combinations of reboot operations for reader and writer DB instances in an Aurora DB cluster\. After each reboot, SQL queries demonstrate the uptime for the instances in the cluster\. 

**Topics**
+ [Finding the writer and reader instances for an Aurora cluster](#USER_Reboot.Examples.IsClusterWriter)
+ [Rebooting a single reader instance](#USER_Reboot.Examples.RebootReader)
+ [Rebooting the writer instance](#USER_Reboot.Examples.RebootWriter)
+ [Rebooting the writer and readers independently](#USER_Reboot.Examples.RebootAsynch)
+ [Applying a cluster parameter change to an Aurora MySQL version 2\.10 cluster](#USER_Reboot.Examples.ParamChangeNewStyle)

### Finding the writer and reader instances for an Aurora cluster<a name="USER_Reboot.Examples.IsClusterWriter"></a>

 In an Aurora MySQL cluster with multiple DB instances, it's important to know which one is the writer and which ones are the readers\. The writer and reader instances also can switch roles when a failover operation happens\. Thus, it's best to perform a check like the following before doing any operation that requires a writer or reader instance\. In this case, the `False` values for `IsClusterWriter` identify the reader instances, `instance-6305` and `instance-7448`\. The `True` value identifies the writer instance, `instance-1234`\. 

```
$ aws rds describe-db-clusters --db-cluster-id tpch100g \
  --query "*[].['Cluster:',DBClusterIdentifier,DBClusterMembers[*].['Instance:',DBInstanceIdentifier,IsClusterWriter]]" \
  --output text
Cluster:     tpch100g
Instance:    instance-6305    False
Instance:    instance-7448    False
Instance:    instance-1234    True
```

 Before we start the examples of rebooting, the writer instance has an uptime of approximately one week\. The SQL query in this example shows a MySQL\-specific way to check the uptime\. You might use this technique in a database application\. For another technique that uses the AWS CLI and works for both Aurora engines, see [Checking uptime for Aurora clusters and instances](#USER_Reboot.Uptime)\. 

```
$ mysql -h instance-7448.a12345.us-east-1.rds.amazonaws.com -P 3306 -u my-user -p
...
mysql> select date_sub(now(), interval variable_value second) "Last Startup",
    -> time_format(sec_to_time(variable_value),'%Hh %im') as "Uptime"
    -> from performance_schema.global_status
    -> where variable_name='Uptime';
+----------------------------+---------+
| Last Startup               | Uptime  |
+----------------------------+---------+
| 2021-03-08 17:49:06.000000 | 174h 42m|
+----------------------------+---------+
```

### Rebooting a single reader instance<a name="USER_Reboot.Examples.RebootReader"></a>

 This example reboots one of the reader DB instances\. Perhaps this instance was overloaded by a huge query or many concurrent connections\. Or perhaps it fell behind the writer instance because of a network issue\. After starting the reboot operation, the example uses a `wait` command to pause until the instance becomes available\. By that point, the instance has an uptime of a few minutes\. 

```
$ aws rds reboot-db-instance --db-instance-identifier instance-6305
{
    "DBInstance": {
        "DBInstanceIdentifier": "instance-6305",
        "DBInstanceStatus": "rebooting",
...
    }
}
$ aws rds wait db-instance-available --db-instance-id instance-6305
$ mysql -h instance-6305.a12345.us-east-1.rds.amazonaws.com -P 3306 -u my-user -p
...
mysql> select date_sub(now(), interval variable_value second) "Last Startup",
    -> time_format(sec_to_time(variable_value),'%Hh %im') as "Uptime"
    -> from performance_schema.global_status
    -> where variable_name='Uptime';
+----------------------------+---------+
| Last Startup               | Uptime  |
+----------------------------+---------+
| 2021-03-16 00:35:02.000000 | 00h 03m |
+----------------------------+---------+
```

 Rebooting the reader instance didn't affect the uptime of the writer instance\. It still has an uptime of about one week\. 

```
$ mysql -h instance-7448.a12345.us-east-1.rds.amazonaws.com -P 3306 -u my-user -p
...
mysql> select date_sub(now(), interval variable_value second) "Last Startup",
    -> time_format(sec_to_time(variable_value),'%Hh %im') as "Uptime"
    -> from performance_schema.global_status where variable_name='Uptime';
+----------------------------+----------+
| Last Startup               | Uptime   |
+----------------------------+----------+
| 2021-03-08 17:49:06.000000 | 174h 49m |
+----------------------------+----------+
```

### Rebooting the writer instance<a name="USER_Reboot.Examples.RebootWriter"></a>

 This example reboots the writer instance\. This cluster is running Aurora MySQL version 2\.09\. Because the Aurora MySQL version is lower than 2\.10, rebooting the writer instance also reboots any reader instances in the cluster\. 

 A `wait` command pauses until the reboot is finished\. Now the uptime for that instance is reset to zero\. It's possible that a reboot operation might take substantially different times for writer and reader DB instances\. The writer and reader DB instances perform different kinds of cleanup operations depending on their roles\. 

```
$ aws rds reboot-db-instance --db-instance-identifier instance-1234
{
    "DBInstance": {
        "DBInstanceIdentifier": "instance-1234",
        "DBInstanceStatus": "rebooting",
...
    }
}
$ aws rds wait db-instance-available --db-instance-id instance-1234
$ mysql -h instance-1234.a12345.us-east-1.rds.amazonaws.com -P 3306 -u my-user -p
...
mysql> select date_sub(now(), interval variable_value second) "Last Startup",
    -> time_format(sec_to_time(variable_value),'%Hh %im') as "Uptime"
    -> from performance_schema.global_status where variable_name='Uptime';
+----------------------------+---------+
| Last Startup               | Uptime  |
+----------------------------+---------+
| 2021-03-16 00:40:27.000000 | 00h 00m |
+----------------------------+---------+
```

 After the reboot for the writer DB instance, both of the reader DB instances also have their uptime reset\. Rebooting the writer instance caused the reader instances to reboot also\. This behavior applies to Aurora PostgreSQL clusters and to Aurora MySQL clusters before version 2\.10\. 

```
$ mysql -h instance-7448.a12345.us-east-1.rds.amazonaws.com -P 3306 -u my-user -p
...
mysql> select date_sub(now(), interval variable_value second) "Last Startup",
    -> time_format(sec_to_time(variable_value),'%Hh %im') as "Uptime"
    -> from performance_schema.global_status where variable_name='Uptime';
+----------------------------+---------+
| Last Startup               | Uptime  |
+----------------------------+---------+
| 2021-03-16 00:40:35.000000 | 00h 00m |
+----------------------------+---------+

$ mysql -h instance-6305.a12345.us-east-1.rds.amazonaws.com -P 3306 -u my-user -p
...
mysql> select date_sub(now(), interval variable_value second) "Last Startup",
    -> time_format(sec_to_time(variable_value),'%Hh %im') as "Uptime"
    -> from performance_schema.global_status where variable_name='Uptime';
+----------------------------+---------+
| Last Startup               | Uptime  |
+----------------------------+---------+
| 2021-03-16 00:40:33.000000 | 00h 01m |
+----------------------------+---------+
```

### Rebooting the writer and readers independently<a name="USER_Reboot.Examples.RebootAsynch"></a>

 These next examples show a cluster that runs Aurora MySQL version 2\.10\. In this Aurora MySQL version and higher, you can reboot the writer instance without causing reboots for all the reader instances\. That way, your query\-intensive applications don't experience any outage when you reboot the writer instance\. You can reboot the reader instances later\. You might do those reboots at a time of low query traffic\. You might also reboot the reader instances one at a time\. That way, at least one reader instance is always available for the query traffic of your application\. 

 The following example uses a cluster named `cluster-2393`, running Aurora MySQL version `5.7.mysql_aurora.2.10.0`\. This cluster has a writer instance named `instance-9404` and three reader instances named `instance-6772`, `instance-2470`, and `instance-5138`\. 

```
$ aws rds describe-db-clusters --db-cluster-id cluster-2393 \
  --query "*[].['Cluster:',DBClusterIdentifier,DBClusterMembers[*].['Instance:',DBInstanceIdentifier,IsClusterWriter]]" \
  --output text
Cluster:        cluster-2393
Instance:       instance-5138        False
Instance:       instance-2470        False
Instance:       instance-6772        False
Instance:       instance-9404        True
```

 Checking the `uptime` value of each database instance through the `mysql` command shows that each one has roughly the same uptime\. For example, here is the uptime for `instance-5138`\. 

```
mysql> SHOW GLOBAL STATUS LIKE 'uptime';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| Uptime        | 3866  |
+---------------+-------+
```

 By using CloudWatch, we can get the corresponding uptime information without actually logging into the instances\. That way, an administrator can monitor the database but can't view or change any table data\. In this case, we specify a time period spanning five minutes, and check the uptime value every minute\. The increasing uptime values demonstrate that the instances weren't restarted during that period\. 

```
$ aws cloudwatch get-metric-statistics --metric-name "EngineUptime" \
  --start-time "$(date -d '5 minutes ago')" --end-time "$(date -d 'now')" --period 60 \
  --namespace "AWS/RDS" --statistics Minimum --dimensions Name=DBInstanceIdentifier,Value=instance-9404 \
  --output text | sort -k 3
EngineUptime
DATAPOINTS	4648.0	2021-03-17T23:42:00+00:00	Seconds
DATAPOINTS	4708.0	2021-03-17T23:43:00+00:00	Seconds
DATAPOINTS	4768.0	2021-03-17T23:44:00+00:00	Seconds
DATAPOINTS	4828.0	2021-03-17T23:45:00+00:00	Seconds
DATAPOINTS	4888.0	2021-03-17T23:46:00+00:00	Seconds

$ aws cloudwatch get-metric-statistics --metric-name "EngineUptime" \
  --start-time "$(date -d '5 minutes ago')" --end-time "$(date -d 'now')" --period 60 \
  --namespace "AWS/RDS" --statistics Minimum --dimensions Name=DBInstanceIdentifier,Value=instance-6772 \
  --output text | sort -k 3
EngineUptime
DATAPOINTS	4315.0	2021-03-17T23:42:00+00:00	Seconds
DATAPOINTS	4375.0	2021-03-17T23:43:00+00:00	Seconds
DATAPOINTS	4435.0	2021-03-17T23:44:00+00:00	Seconds
DATAPOINTS	4495.0	2021-03-17T23:45:00+00:00	Seconds
DATAPOINTS	4555.0	2021-03-17T23:46:00+00:00	Seconds
```

 Now we reboot one of the reader instances, `instance-5138`\. We wait for the instance to become available again after the reboot\. Now monitoring the uptime over a five\-minute period shows that the uptime was reset to zero during that time\. The most recent uptime value was measured five seconds after the reboot finished\. 

```
$ aws rds reboot-db-instance --db-instance-identifier instance-5138
{
  "DBInstanceIdentifier": "instance-5138",
  "DBInstanceStatus": "rebooting"
}
$ aws rds wait db-instance-available --db-instance-id instance-5138

$ aws cloudwatch get-metric-statistics --metric-name "EngineUptime" \
  --start-time "$(date -d '5 minutes ago')" --end-time "$(date -d 'now')" --period 60 \
  --namespace "AWS/RDS" --statistics Minimum --dimensions Name=DBInstanceIdentifier,Value=instance-5138 \
  --output text | sort -k 3
EngineUptime
DATAPOINTS	4500.0	2021-03-17T23:46:00+00:00	Seconds
DATAPOINTS	4560.0	2021-03-17T23:47:00+00:00	Seconds
DATAPOINTS	4620.0	2021-03-17T23:48:00+00:00	Seconds
DATAPOINTS	4680.0	2021-03-17T23:49:00+00:00	Seconds
DATAPOINTS  5.0 2021-03-17T23:50:00+00:00 Seconds
```

 Next, we perform a reboot for the writer instance, `instance-9404`\. We compare the uptime values for the writer instance and one of the reader instances\. By doing so, we can see that rebooting the writer didn't cause a reboot for the readers\. In versions before Aurora MySQL 2\.10, the uptime values for all the readers would be reset at the same time as the writer\. 

```
$ aws rds reboot-db-instance --db-instance-identifier instance-9404
{
  "DBInstanceIdentifier": "instance-9404",
  "DBInstanceStatus": "rebooting"
}
$ aws rds wait db-instance-available --db-instance-id instance-9404

$ aws cloudwatch get-metric-statistics --metric-name "EngineUptime" \
  --start-time "$(date -d '5 minutes ago')" --end-time "$(date -d 'now')" --period 60 \
  --namespace "AWS/RDS" --statistics Minimum --dimensions Name=DBInstanceIdentifier,Value=instance-9404 \
  --output text | sort -k 3
EngineUptime
DATAPOINTS	371.0	2021-03-17T23:57:00+00:00	Seconds
DATAPOINTS	431.0	2021-03-17T23:58:00+00:00	Seconds
DATAPOINTS	491.0	2021-03-17T23:59:00+00:00	Seconds
DATAPOINTS	551.0	2021-03-18T00:00:00+00:00	Seconds
DATAPOINTS  37.0  2021-03-18T00:01:00+00:00 Seconds

$ aws cloudwatch get-metric-statistics --metric-name "EngineUptime" \
  --start-time "$(date -d '5 minutes ago')" --end-time "$(date -d 'now')" --period 60 \
  --namespace "AWS/RDS" --statistics Minimum --dimensions Name=DBInstanceIdentifier,Value=instance-6772 \
  --output text | sort -k 3
EngineUptime
DATAPOINTS	5215.0	2021-03-17T23:57:00+00:00	Seconds
DATAPOINTS	5275.0	2021-03-17T23:58:00+00:00	Seconds
DATAPOINTS	5335.0	2021-03-17T23:59:00+00:00	Seconds
DATAPOINTS	5395.0	2021-03-18T00:00:00+00:00	Seconds
DATAPOINTS	5455.0	2021-03-18T00:01:00+00:00	Seconds
```

 To make sure that all the reader instances have all the same changes to configuration parameters as the writer instance, reboot all the reader instances after the writer\. This example reboots all the readers and then waits until all of them are available before proceeding\. 

```
$ aws rds reboot-db-instance --db-instance-identifier instance-6772
{
  "DBInstanceIdentifier": "instance-6772",
  "DBInstanceStatus": "rebooting"
}

$ aws rds reboot-db-instance --db-instance-identifier instance-2470
{
  "DBInstanceIdentifier": "instance-2470",
  "DBInstanceStatus": "rebooting"
}

$ aws rds reboot-db-instance --db-instance-identifier instance-5138
{
  "DBInstanceIdentifier": "instance-5138",
  "DBInstanceStatus": "rebooting"
}

$ aws rds wait db-instance-available --db-instance-id instance-6772
$ aws rds wait db-instance-available --db-instance-id instance-2470
$ aws rds wait db-instance-available --db-instance-id instance-5138
```

 Now we can see that the writer DB instance has the highest uptime\. This instance's uptime value increased steadily throughout the monitoring period\. The reader DB instances were all rebooted after the reader\. We can see the point within the monitoring period when each reader was rebooted and its uptime was reset to zero\. 

```
$ aws cloudwatch get-metric-statistics --metric-name "EngineUptime" \
  --start-time "$(date -d '5 minutes ago')" --end-time "$(date -d 'now')" --period 60 \
  --namespace "AWS/RDS" --statistics Minimum --dimensions Name=DBInstanceIdentifier,Value=instance-9404 \
  --output text | sort -k 3
EngineUptime
DATAPOINTS	457.0	2021-03-18T00:08:00+00:00	Seconds
DATAPOINTS	517.0	2021-03-18T00:09:00+00:00	Seconds
DATAPOINTS	577.0	2021-03-18T00:10:00+00:00	Seconds
DATAPOINTS	637.0	2021-03-18T00:11:00+00:00	Seconds
DATAPOINTS  697.0 2021-03-18T00:12:00+00:00 Seconds

$ aws cloudwatch get-metric-statistics --metric-name "EngineUptime" \
  --start-time "$(date -d '5 minutes ago')" --end-time "$(date -d 'now')" --period 60 \
  --namespace "AWS/RDS" --statistics Minimum --dimensions Name=DBInstanceIdentifier,Value=instance-2470 \
  --output text | sort -k 3
EngineUptime
DATAPOINTS	5819.0	2021-03-18T00:08:00+00:00	Seconds
DATAPOINTS  35.0  2021-03-18T00:09:00+00:00 Seconds
DATAPOINTS	95.0	2021-03-18T00:10:00+00:00	Seconds
DATAPOINTS	155.0	2021-03-18T00:11:00+00:00	Seconds
DATAPOINTS	215.0	2021-03-18T00:12:00+00:00	Seconds

$ aws cloudwatch get-metric-statistics --metric-name "EngineUptime" \
  --start-time "$(date -d '5 minutes ago')" --end-time "$(date -d 'now')" --period 60 \
  --namespace "AWS/RDS" --statistics Minimum --dimensions Name=DBInstanceIdentifier,Value=instance-5138 \
  --output text | sort -k 3
EngineUptime
DATAPOINTS	1085.0	2021-03-18T00:08:00+00:00	Seconds
DATAPOINTS	1145.0	2021-03-18T00:09:00+00:00	Seconds
DATAPOINTS	1205.0	2021-03-18T00:10:00+00:00	Seconds
DATAPOINTS  49.0  2021-03-18T00:11:00+00:00 Seconds
DATAPOINTS	109.0	2021-03-18T00:12:00+00:00	Seconds
```

### Applying a cluster parameter change to an Aurora MySQL version 2\.10 cluster<a name="USER_Reboot.Examples.ParamChangeNewStyle"></a>

 The following example demonstrates how to apply a parameter change to all DB instances in your Aurora MySQL 2\.10 cluster\. With this Aurora MySQL version, you reboot the writer instance and all the reader instances independently\. 

 The example uses the MySQL configuration parameter `lower_case_table_names` for illustration\. When this parameter setting is different between the writer and reader DB instances, a query might not be able to access a table declared with an uppercase or mixed\-case name\. Or if two table names differ only in terms of uppercase and lowercase letters, a query might access the wrong table\. 

 This example shows how to determine the writer and reader instances in the cluster by examining the `IsClusterWriter` attribute of each instance\. The cluster is named `cluster-2393`\. The cluster has a writer instance named `instance-9404`\. The reader instances in the cluster are named `instance-5138` and `instance-2470`\. 

```
$ aws rds describe-db-clusters --db-cluster-id cluster-2393 \
  --query '*[].[DBClusterIdentifier,DBClusterMembers[*].[DBInstanceIdentifier,IsClusterWriter]]' \
  --output text
cluster-2393
instance-5138        False
instance-2470        False
instance-9404        True
```

 To demonstrate the effects of changing the `lower_case_table_names` parameter, we set up two DB cluster parameter groups\. The `lower-case-table-names-0` parameter group has this parameter set to 0\. The `lower-case-table-names-1` parameter group has this parameter group set to 1\. 

```
$ aws rds create-db-cluster-parameter-group --description 'lower-case-table-names-0' \
  --db-parameter-group-family aurora-mysql5.7 \
  --db-cluster-parameter-group-name lower-case-table-names-0
{
    "DBClusterParameterGroup": {
        "DBClusterParameterGroupName": "lower-case-table-names-0",
        "DBParameterGroupFamily": "aurora-mysql5.7",
        "Description": "lower-case-table-names-0"
    }
}

$ aws rds create-db-cluster-parameter-group --description 'lower-case-table-names-1' \
  --db-parameter-group-family aurora-mysql5.7 \
  --db-cluster-parameter-group-name lower-case-table-names-1
{
    "DBClusterParameterGroup": {
        "DBClusterParameterGroupName": "lower-case-table-names-1",
        "DBParameterGroupFamily": "aurora-mysql5.7",
        "Description": "lower-case-table-names-1"
    }
}

$ aws rds modify-db-cluster-parameter-group \
  --db-cluster-parameter-group-name lower-case-table-names-0 \
  --parameters ParameterName=lower_case_table_names,ParameterValue=0,ApplyMethod=pending-reboot
{
    "DBClusterParameterGroupName": "lower-case-table-names-0"
}

$ aws rds modify-db-cluster-parameter-group \
  --db-cluster-parameter-group-name lower-case-table-names-1 \
    --parameters ParameterName=lower_case_table_names,ParameterValue=1,ApplyMethod=pending-reboot
{
    "DBClusterParameterGroupName": "lower-case-table-names-1"
}
```

 The default value of `lower_case_table_names` is 0\. With this parameter setting, the table `foo` is distinct from the table `FOO`\. This example verifies that the parameter is still at its default setting\. Then the example creates three tables that differ only in uppercase and lowercase letters in their names\. 

```
mysql> create database lctn;
Query OK, 1 row affected (0.07 sec)

mysql> use lctn;
Database changed
mysql> select @@lower_case_table_names;
+--------------------------+
| @@lower_case_table_names |
+--------------------------+
|                        0 |
+--------------------------+

mysql> create table foo (s varchar(128));
mysql> insert into foo values ('Lowercase table name foo');

mysql> create table Foo (s varchar(128));
mysql> insert into Foo values ('Mixed-case table name Foo');

mysql> create table FOO (s varchar(128));
mysql> insert into FOO values ('Uppercase table name FOO');

mysql> select * from foo;
+--------------------------+
| s                        |
+--------------------------+
| Lowercase table name foo |
+--------------------------+

mysql> select * from Foo;
+---------------------------+
| s                         |
+---------------------------+
| Mixed-case table name Foo |
+---------------------------+

mysql> select * from FOO;
+--------------------------+
| s                        |
+--------------------------+
| Uppercase table name FOO |
+--------------------------+
```

 Next, we associate the DB parameter group with the cluster to set the `lower_case_table_names` parameter to 1\. This change only takes effect after each DB instance is rebooted\. 

```
$ aws rds modify-db-cluster --db-cluster-identifier cluster-2393 \
  --db-cluster-parameter-group-name lower-case-table-names-1
{
  "DBClusterIdentifier": "cluster-2393",
  "DBClusterParameterGroup": "lower-case-table-names-1",
  "Engine": "aurora-mysql",
  "EngineVersion": "5.7.mysql_aurora.2.10.0"
}
```

 The first reboot we do is for the writer DB instance\. Then we wait for the instance to become available again\. At that point, we connect to the writer endpoint and verify that the writer instance has the changed parameter value\. The `SHOW TABLES` command confirms that the database contains the three different tables\. However, queries that refer to tables named `foo`, `Foo`, or `FOO` all access the table whose name is all\-lowercase, `foo`\. 

```
# Rebooting the writer instance
$ aws rds reboot-db-instance --db-instance-identifier instance-9404
$ aws rds wait db-instance-available --db-instance-id instance-9404
```

 Now, queries using the cluster endpoint show the effects of the parameter change\. Whether the table name in the query is uppercase, lowercase, or mixed case, the SQL statement accesses the table whose name is all lowercase\. 

```
mysql> select @@lower_case_table_names;
+--------------------------+
| @@lower_case_table_names |
+--------------------------+
|                        1 |
+--------------------------+

mysql> use lctn;
mysql> show tables;
+----------------+
| Tables_in_lctn |
+----------------+
| FOO            |
| Foo            |
| foo            |
+----------------+

mysql> select * from foo;
+--------------------------+
| s                        |
+--------------------------+
| Lowercase table name foo |
+--------------------------+

mysql> select * from Foo;
+--------------------------+
| s                        |
+--------------------------+
| Lowercase table name foo |
+--------------------------+

mysql> select * from FOO;
+--------------------------+
| s                        |
+--------------------------+
| Lowercase table name foo |
+--------------------------+
```

 The next example shows the same queries as the previous one\. In this case, the queries use the reader endpoint and run on one of the reader DB instances\. Those instances haven't been rebooted yet\. Thus, they still have the original setting for the `lower_case_table_names` parameter\. That means that queries can access each of the `foo`, `Foo`, and `FOO` tables\. 

```
mysql> select @@lower_case_table_names;
+--------------------------+
| @@lower_case_table_names |
+--------------------------+
|                        0 |
+--------------------------+

mysql> use lctn;

mysql> select * from foo;
+--------------------------+
| s                        |
+--------------------------+
| Lowercase table name foo |
+--------------------------+

mysql> select * from Foo;
+---------------------------+
| s                         |
+---------------------------+
| Mixed-case table name Foo |
+---------------------------+

mysql> select * from FOO;
+--------------------------+
| s                        |
+--------------------------+
| Uppercase table name FOO |
+--------------------------+
```

 Next, we reboot one of the reader instances and wait for it to become available again\. 

```
$ aws rds reboot-db-instance --db-instance-identifier instance-2470
{
  "DBInstanceIdentifier": "instance-2470",
  "DBInstanceStatus": "rebooting"
}
$ aws rds wait db-instance-available --db-instance-id instance-2470
```

 While connected to the instance endpoint for `instance-2470`, a query shows that the new parameter is in effect\. 

```
mysql> select @@lower_case_table_names;
+--------------------------+
| @@lower_case_table_names |
+--------------------------+
|                        1 |
+--------------------------+
```

 At this point, the two reader instances in the cluster are running with different `lower_case_table_names` settings\. Thus, any connection to the reader endpoint of the cluster uses a value for this setting that's unpredictable\. It's important to immediately reboot the other reader instance so that they both have consistent settings\. 

```
$ aws rds reboot-db-instance --db-instance-identifier instance-5138
{
  "DBInstanceIdentifier": "instance-5138",
  "DBInstanceStatus": "rebooting"
}
$ aws rds wait db-instance-available --db-instance-id instance-5138
```

 The following example confirms that all the reader instances have the same setting for the `lower_case_table_names` parameter\. The commands check the `lower_case_table_names` setting value on each reader instance\. Then the same command using the reader endpoint demonstrates that each connection to the reader endpoint uses one of the reader instances, but which one isn't predictable\. 

```
# Check lower_case_table_names setting on each reader instance.

$ mysql -h instance-5138.a12345.us-east-1.rds.amazonaws.com \
  -u my-user -p -e 'select @@aurora_server_id, @@lower_case_table_names'
+--------------------------+--------------------------+
| @@aurora_server_id       | @@lower_case_table_names |
+--------------------------+--------------------------+
| instance-5138            |                        1 |
+--------------------------+--------------------------+

$ mysql -h instance-2470.a12345.us-east-1.rds.amazonaws.com \
  -u my-user -p -e 'select @@aurora_server_id, @@lower_case_table_names'
+--------------------------+--------------------------+
| @@aurora_server_id       | @@lower_case_table_names |
+--------------------------+--------------------------+
| instance-2470            |                        1 |
+--------------------------+--------------------------+

# Check lower_case_table_names setting on the reader endpoint of the cluster.

$ mysql -h cluster-2393.cluster-ro-a12345.us-east-1.rds.amazonaws.com \
  -u my-user -p -e 'select @@aurora_server_id, @@lower_case_table_names'
+--------------------------+--------------------------+
| @@aurora_server_id       | @@lower_case_table_names |
+--------------------------+--------------------------+
| instance-5138            |                        1 |
+--------------------------+--------------------------+

# Run query on writer instance

$ mysql -h cluster-2393.cluster-a12345.us-east-1.rds.amazonaws.com \
  -u my-user -p -e 'select @@aurora_server_id, @@lower_case_table_names'
+--------------------------+--------------------------+
| @@aurora_server_id       | @@lower_case_table_names |
+--------------------------+--------------------------+
| instance-9404            |                        1 |
+--------------------------+--------------------------+
```

 With the parameter change applied everywhere, we can see the effect of setting `lower_case_table_names=1`\. Whether the table is referred to as `foo`, `Foo`, or `FOO` the query converts the name to `foo` and accesses the same table in each case\. 

```
mysql> use lctn;

mysql> select * from foo;
+--------------------------+
| s                        |
+--------------------------+
| Lowercase table name foo |
+--------------------------+

mysql> select * from Foo;
+--------------------------+
| s                        |
+--------------------------+
| Lowercase table name foo |
+--------------------------+

mysql> select * from FOO;
+--------------------------+
| s                        |
+--------------------------+
| Lowercase table name foo |
+--------------------------+
```