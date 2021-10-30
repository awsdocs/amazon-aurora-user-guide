# Client:ClientRead<a name="apg-waits.clientread"></a>

The `Client:ClientRead` event occurs when Aurora PostgreSQL is waiting to receive data from the client\.

**Topics**
+ [Supported engine versions](#apg-waits.clientread.context.supported)
+ [Context](#apg-waits.clientread.context)
+ [Likely causes of increased waits](#apg-waits.clientread.causes)
+ [Actions](#apg-waits.clientread.actions)

## Supported engine versions<a name="apg-waits.clientread.context.supported"></a>

This wait event information is supported for Aurora PostgreSQL version 10 and higher\.

## Context<a name="apg-waits.clientread.context"></a>

An Aurora PostgreSQL DB cluster is waiting to receive data from the client\. The Aurora PostgreSQL DB cluster must receive the data from the client before it can send more data to the client\. The time that the cluster waits before receiving data from the client is a `Client:ClientRead` event\.

## Likely causes of increased waits<a name="apg-waits.clientread.causes"></a>

Common causes for the `Client:ClientRead` event to appear in top waits include the following: 

**Increased network latency**  
There might be increased network latency between the Aurora PostgreSQL DB cluster and client\. Higher network latency increases the time required for DB cluster to receive data from the client\.

**Increased load on the client**  
There might be CPU pressure or network saturation on the client\. An increase in load on the client can delay transmission of data from the client to the Aurora PostgreSQL DB cluster\.

**Excessive network round trips**  
A large number of network round trips between the Aurora PostgreSQL DB cluster and the client can delay transmission of data from the client to the Aurora PostgreSQL DB cluster\.

**Large copy operation**  
During a copy operation, the data is transferred from the client's file system to the Aurora PostgreSQL DB cluster\. Sending a large amount of data to the DB cluster can delay transmission of data from the client to the DB cluster\.

**Idle client connection**  
When a client connects to the Aurora PostgreSQL DB cluster in an `idle in transaction` state, the DB cluster might wait for the client to send more data or issue a command\. A connection in this state can lead to an increase in `Client:ClientRead` events\.

**PgBouncer used for connection pooling**  
PgBouncer has a low\-level network configuration setting called `pkt_buf`, which is set to 4,096 by default\. If the workload is sending query packets larger than 4,096 bytes through PgBouncer, we recommend increasing the `pkt_buf` setting to 8,192\. If the new setting doesn't decrease the number of `Client:ClientRead` events, we recommend increasing the `pkt_buf` setting to larger values, such as 16,384 or 32,768\. If the query text is large, the larger setting can be particularly helpful\.

## Actions<a name="apg-waits.clientread.actions"></a>

We recommend different actions depending on the causes of your wait event\.

**Topics**
+ [Place the clients in the same Availability Zone and VPC subnet as the cluster](#apg-waits.clientread.actions.az-vpc-subnet)
+ [Scale your client](#apg-waits.clientread.actions.scale-client)
+ [Use current generation instances](#apg-waits.clientread.actions.db-instance-class)
+ [Increase network bandwidth](#apg-waits.clientread.actions.increase-network-bandwidth)
+ [Monitor maximums for network performance](#apg-waits.clientread.actions.monitor-network-performance)
+ [Monitor for transactions in the "idle in transaction" state](#apg-waits.clientread.actions.check-idle-in-transaction)

### Place the clients in the same Availability Zone and VPC subnet as the cluster<a name="apg-waits.clientread.actions.az-vpc-subnet"></a>

To reduce network latency and increase network throughput, place clients in the same Availability Zone and virtual private cloud \(VPC\) subnet as the Aurora PostgreSQL DB cluster\. Make sure that the clients are as geographically close to the DB cluster as possible\.

### Scale your client<a name="apg-waits.clientread.actions.scale-client"></a>

Using Amazon CloudWatch or other host metrics, determine if your client is currently constrained by CPU or network bandwidth, or both\. If the client is constrained, scale your client accordingly\.

### Use current generation instances<a name="apg-waits.clientread.actions.db-instance-class"></a>

In some cases, you might not be using a DB instance class that supports jumbo frames\. If you're running your application on Amazon EC2, consider using a current generation instance for the client\. Also, configure the maximum transmission unit \(MTU\) on the client operating system\. This technique might reduce the number of network round trips and increase network throughput\. For more information, see [ Jumbo frames \(9001 MTU\)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/network_mtu.html#jumbo_frame_instances) in the *Amazon EC2 User Guide for Linux Instances*\.

For information about DB instance classes, see [Aurora DB instance classes](Concepts.DBInstanceClass.md)\. To determine the DB instance class that is equivalent to an Amazon EC2 instance type, place `db.` before the Amazon EC2 instance type name\. For example, the `r5.8xlarge` Amazon EC2 instance is equivalent to the `db.r5.8xlarge` DB instance class\.

### Increase network bandwidth<a name="apg-waits.clientread.actions.increase-network-bandwidth"></a>

Use `NetworkReceiveThroughput` and `NetworkTransmitThroughput` Amazon CloudWatch metrics to monitor incoming and outgoing network traffic on the DB cluster\. These metrics can help you to determine if network bandwidth is sufficient for your workload\. 

If your network bandwidth isn't enough, increase it\. If the AWS client or your DB instance is reaching the network bandwidth limits, the only way to increase the bandwidth is to increase your DB instance size\.

For more information about CloudWatch metrics, see [Amazon Aurora metrics](Aurora.AuroraMySQL.Monitoring.Metrics.md)\.

### Monitor maximums for network performance<a name="apg-waits.clientread.actions.monitor-network-performance"></a>

If you are using Amazon EC2 clients, Amazon EC2 provides maximums for network performance metrics, including aggregate inbound and outbound network bandwidth\. It also provides connection tracking to ensure that packets are returned as expected and link\-local services access for services such as the Domain Name System \(DNS\)\. To monitor these maximums, use a current enhanced networking driver and monitor network performance for your client\. 

For more information, see [ Monitor network performance for your Amazon EC2 instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/monitoring-network-performance-ena.html) in the *Amazon EC2 User Guide for Linux Instances* and [Monitor network performance for your Amazon EC2 instance](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/monitoring-network-performance-ena.html) in the *Amazon EC2 User Guide for Windows Instances*\.

### Monitor for transactions in the "idle in transaction" state<a name="apg-waits.clientread.actions.check-idle-in-transaction"></a>

Check whether you have an increasing number of `idle in transaction` connections\. To do this, monitor the `state` column in the `pg_stat_activity` table\. You might be able to identify the connection source by running a query similar to the following\.

```
select client_addr, state, count(1) from pg_stat_activity 
where state like 'idle in transaction%' 
group by 1,2 
order by 3 desc
```