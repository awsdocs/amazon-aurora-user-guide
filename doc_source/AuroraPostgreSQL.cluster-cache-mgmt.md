# Fast Recovery After Failover with Cluster Cache Management for Aurora PostgreSQL<a name="AuroraPostgreSQL.cluster-cache-mgmt"></a>

For fast recovery of the writer DB instance in your Aurora PostgreSQL clusters if there's a failover, use cluster cache management for Amazon Aurora PostgreSQL\. Cluster cache management ensures that application performance is maintained if there's a failover\. 

In a typical failover situation, you might see a temporary but large performance degradation after failover\. This degradation occurs because when the failover DB instance starts, the buffer cache is empty\. An empty cache is also known as a *cold cache*\. A cold cache degrades performance because the DB instance has to read from the slower disk, instead of taking advantage of values stored in the buffer cache\. 

With cluster cache management, you set a specific reader DB instance as the failover target\. Cluster cache management ensures that the data in the designated reader's cache is kept synchronized with the data in the writer DB instance's cache\. The designated reader's cache with prefilled values is known as a *warm cache*\. If a failover occurs, the designated reader uses values in its warm cache immediately when it's promoted to the new writer DB instance\. This approach provides your application much better recovery performance\.

**Topics**
+ [Configuring Cluster Cache Management](#AuroraPostgreSQL.cluster-cache-mgmt.Configure)
+ [Monitoring the Buffer Cache](#AuroraPostgreSQL.cluster-cache-mgmt.Monitoring)

## Configuring Cluster Cache Management<a name="AuroraPostgreSQL.cluster-cache-mgmt.Configure"></a>

To configure cluster cache management, take the following steps\.

**Topics**
+ [Enable Cluster Cache Management](#AuroraPostgreSQL.cluster-cache-mgmt.Enable)
+ [Set the Promotion Tier Priority for the Writer DB Instance](#AuroraPostgreSQL.cluster-cache-mgmt.Writer)
+ [Set the Promotion Tier Priority for a Reader DB Instance](#AuroraPostgreSQL.cluster-cache-mgmt.Reader)

### Enable Cluster Cache Management<a name="AuroraPostgreSQL.cluster-cache-mgmt.Enable"></a>

To enable cluster cache management, set the `apg_ccm_enabled` cluster parameter to `1` as described following\. 

#### AWS Management Console<a name="AuroraPostgreSQL.cluster-cache-mgmt.Enable.CON"></a>

**To enable cluster cache management**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Parameter groups**\.

1. In the list, choose the parameter group for your Aurora PostgreSQL DB cluster\.

   The DB cluster must use a parameter group other than the default, because you can't change values in a default parameter group\. 

1. For **Parameter group actions**, choose **Edit**\.

1. Set the value of the `apg_ccm_enabled` cluster parameter to **1**\.

1. Choose **Save changes**\.

#### AWS CLI<a name="AuroraPostgreSQL.cluster-cache-mgmt.Enable.CLI"></a>

To enable cluster cache management for an Aurora PostgreSQL DB cluster, use the AWS CLI [ `modify-db-cluster-parameter-group`](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster-parameter-group.html) command with the following required parameters:
+ `--db-cluster-parameter-group-name`
+ `--parameters`

**Example**  
For Linux, OS X, or Unix:  

```
aws rds modify-db-cluster-parameter-group \
    --db-cluster-parameter-group-name mydbclusterparametergroup \
    --parameters "ParameterName=apg_ccm_enabled,ParameterValue=1,ApplyMethod=immediate"
```
For Windows:  

```
aws rds modify-db-cluster-parameter-group ^
    --db-cluster-parameter-group-name mydbclusterparametergroup ^
    --parameters "ParameterName=apg_ccm_enabled,ParameterValue=1,ApplyMethod=immediate"
```

### Set the Promotion Tier Priority for the Writer DB Instance<a name="AuroraPostgreSQL.cluster-cache-mgmt.Writer"></a>

Make sure that the promotion priority is **tier\-0** for the writer DB instance of the Aurora PostgreSQL DB cluster\. The *promotion tier priority *is a value that specifies the order in which an Aurora reader is promoted to the writer DB instance after a failure\. Valid values are 0–15, where 0 is the first priority and 15 is the last priority\. For more information about the promotion tier, see [Fault Tolerance for an Aurora DB Cluster](Aurora.Managing.Backups.md#Aurora.Managing.FaultTolerance)\. 

#### AWS Management Console<a name="AuroraPostgreSQL.cluster-cache-mgmt.Writer.Console"></a>

**To set the promotion priority for the writer DB instance to tier\-0**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the **Writer** DB instance of the Aurora PostgreSQL DB cluster\. 

1. Choose **Modify**\. The **Modify DB Instance** page appears\.

1. On the **Failover** panel, choose **tier\-0** for the **Priority**\. 

1. Choose **Continue** and check the summary of modifications\. 

1. To apply the changes immediately after you save them, choose **Apply immediately**\. 

1. Choose **Modify DB Instance** to save your changes\. 

#### AWS CLI<a name="AuroraPostgreSQL.cluster-cache-mgmt.Writer.CLI"></a>

To set the promotion tier priority to 0 for the writer DB instance using the AWS CLI, call the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command with the following required parameters:
+ `--db-instance-identifier`
+ `--promotion-tier`
+ `--apply-immediately`

**Example**  
For Linux, OS X, or Unix:  

```
aws rds modify-db-instance \
    --db-instance-identifier mydbinstance \
    --promotion-tier 0 \
    --apply-immediately
```
For Windows:  

```
aws rds modify-db-instance ^
    --db-instance-identifier mydbinstance ^
    ---promotion-tier 0  ^
    --apply-immediately
```

### Set the Promotion Tier Priority for a Reader DB Instance<a name="AuroraPostgreSQL.cluster-cache-mgmt.Reader"></a>

You set one reader DB instance for cluster cache management\. To do so, choose a reader from the Aurora PostgreSQL cluster that is the same instance type as the writer DB instance\. Then set its promotion tier priority to 0\. 

 The *promotion tier priority *is a value that specifies the order in which an Aurora reader is promoted to the writer DB instance after a failure\. Valid values are 0–15, where 0 is the first priority and 15 is the last priority\.

#### AWS Management Console<a name="AuroraPostgreSQL.cluster-cache-mgmt.Reader.Console"></a>

**To set the promotion priority of the reader DB instance to tier\-0**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\. 

1. Choose a **Reader** DB instance of the Aurora PostgreSQL DB cluster that is the same instance type as the writer DB instance\. 

1. Choose **Modify**\. The **Modify DB Instance** page appears\.

1. On the **Failover** panel, choose **tier\-0** for the **Priority**\. 

1. Choose **Continue** and check the summary of modifications\. 

1. To apply the changes immediately after you save them, choose **Apply immediately**\. 

1. Choose **Modify DB Instance** to save your changes\. 

#### AWS CLI<a name="AuroraPostgreSQL.cluster-cache-mgmt.Reader.CLI"></a>

To set the promotion tier priority to 0 for the reader DB instance using the AWS CLI, call the [modify\-db\-instance](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-instance.html) command with the following required parameters:
+ `--db-instance-identifier`
+ `--promotion-tier`
+ `--apply-immediately`

**Example**  
For Linux, OS X, or Unix:  

```
aws rds modify-db-instance \
    --db-instance-identifier mydbinstance \
    --promotion-tier 0 \
    --apply-immediately
```
For Windows:  

```
aws rds modify-db-instance ^
    --db-instance-identifier mydbinstance ^
    ---promotion-tier 0  ^
    --apply-immediately
```

## Monitoring the Buffer Cache<a name="AuroraPostgreSQL.cluster-cache-mgmt.Monitoring"></a>

Cluster cache management ensures that the designated reader's warm buffer cache is kept synchronized with the writer DB instance's buffer cache\. To examine the buffer cache contents on both the writer DB instance and the designated reader DB instance, use the PostgreSQL `pg_buffercache` module For more information, see the [PostgreSQL `pg_buffercache` documentation](https://www.postgresql.org/docs/current/pgbuffercache.html)\. 

Cluster cache management also provides the `apg_ccm_status` function\. Use the `apg_ccm_status` function on the writer DB instance to get the following information about the progress of cache warming on the designated reader:
+ `buffers_sent_last_minute` – How many buffers have been sent to the designated reader in the last minute\.
+ `buffers_sent_last_scan` – How many buffers have been sent to the designated reader during the last complete scan of the buffer cache\.
+ `buffers_found_last_scan` – How many buffers have been identified as frequently accessed and needed to be sent during the last complete scan of the buffer cache\. Buffers already cached on the designated reader aren't sent\.
+ `buffers_sent_current_scan` – How many buffers have been sent so far during the current scan\.
+ `buffers_found_current_scan` – How many buffers have been identified as frequently accessed in the current scan\.
+ `current_scan_progress` – How many buffers have been visited so far during the current scan\.

The following example shows how to use the `apg_ccm_status` function to convert some of its output into a warm rate and warm percentage\.

```
SELECT buffers_sent_last_minute*8/60 AS warm_rate_kbps, 
   100*(1.0-buffers_sent_last_scan/buffers_found_last_scan) AS warm_percent 
   FROM apg_ccm_status();
```