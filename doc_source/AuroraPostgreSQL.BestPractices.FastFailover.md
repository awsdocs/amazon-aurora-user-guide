# Fast failover with Amazon Aurora PostgreSQL<a name="AuroraPostgreSQL.BestPractices.FastFailover"></a>

Following, you can learn how to make sure that failover occurs as fast as possible\. To recover quickly after failover, you can use cluster cache management for your Aurora PostgreSQL DB cluster\. For more information, see [Fast recovery after failover with cluster cache management for Aurora PostgreSQL](AuroraPostgreSQL.cluster-cache-mgmt.md)\. 

Some of the steps that you can take to make failover perform fast include the following:
+ Set Transmission Control Protocol \(TCP\) keepalives with short time frames, to stop longer running queries before the read timeout expires if there's a failure\.
+ Set timeouts for Java Domain Name System \(DNS\) caching aggressively\. Doing this helps ensure the Aurora read\-only endpoint can properly cycle through read\-only nodes on later connection attempts\. 
+ Set the timeout variables used in the JDBC connection string as low as possible\. Use separate connection objects for short\- and long\-running queries\.
+ Use the read and write Aurora endpoints that are provided to connect to the cluster\.
+ Use RDS API operations to test application response on server\-side failures\. Also, use a packet dropping tool to test application response for client\-side failures\.
+ Use the AWS JDBC Driver for PostgreSQL \(preview\) to take full advantage of the failover capabilities of Aurora PostgreSQL\. For more information about the AWS JDBC Driver for PostgreSQL and complete instructions for using it, see the [AWS JDBC Driver for PostgreSQL GitHub repository](https://awslabs.github.io/aws-postgresql-jdbc/)\.

These are covered in more detail following\. 

**Topics**
+ [Setting TCP keepalives parameters](#AuroraPostgreSQL.BestPractices.FastFailover.TCPKeepalives)
+ [Configuring your application for fast failover](#AuroraPostgreSQL.BestPractices.FastFailover.Configuring)
+ [Testing failover](#AuroraPostgreSQL.BestPracticesFastFailover.Testing)
+ [Fast failover example in Java](#AuroraPostgreSQL.BestPractices.FastFailover.Example)

## Setting TCP keepalives parameters<a name="AuroraPostgreSQL.BestPractices.FastFailover.TCPKeepalives"></a>

When you set up a TCP connection, a set of timers is associated with the connection\. When the keepalive timer reaches zero, a keepalive probe packet is sent to the connection endpoint\. If the probe receives a reply, you can assume that the connection is still up and running\.

Turning on TCP keepalive parameters and setting them aggressively ensures that if your client can't connect to the database, any active connections are quickly closed\. The application can then connect to a new endpoint\.

Make sure to set the following TCP keepalive parameters:
+ `tcp_keepalive_time` controls the time, in seconds, after which a keepalive packet is sent when no data has been sent by the socket\. ACKs aren't considered data\. We recommend the following setting: 

   `tcp_keepalive_time = 1`
+ `tcp_keepalive_intvl` controls the time, in seconds, between sending subsequent keepalive packets after the initial packet is sent\. Set this time by using the `tcp_keepalive_time` parameter\. We recommend the following setting:

  `tcp_keepalive_intvl = 1`
+ `tcp_keepalive_probes` is the number of unacknowledged keepalive probes that occur before the application is notified\. We recommend the following setting: 

  `tcp_keepalive_probes = 5`

These settings should notify the application within five seconds when the database stops responding\. If keepalive packets are often dropped within the application's network, you can set a higher `tcp_keepalive_probes` value\. Doing this allows for more buffer in less reliable networks, although it increases the time that it takes to detect an actual failure\.

**To set TCP keepalive parameters on Linux**

1. Test how to configure your TCP keepalive parameters\.

   We recommend doing so by using the command line with the following commands\. This suggested configuration is system\-wide\. In other words, it also affects all other applications that create sockets with the `SO_KEEPALIVE` option on\.

   ```
   sudo sysctl net.ipv4.tcp_keepalive_time=1
   sudo sysctl net.ipv4.tcp_keepalive_intvl=1
   sudo sysctl net.ipv4.tcp_keepalive_probes=5
   ```

1. After you've found a configuration that works for your application, persist these settings by adding the following lines to `/etc/sysctl.conf`, including any changes you made:

   ```
   tcp_keepalive_time = 1
   tcp_keepalive_intvl = 1
   tcp_keepalive_probes = 5
   ```

## Configuring your application for fast failover<a name="AuroraPostgreSQL.BestPractices.FastFailover.Configuring"></a>

Following, you can find a discussion of several configuration changes for Aurora PostgreSQL that you can make for fast failover\. To learn more about PostgreSQL JDBC driver setup and configuration, see the [PostgreSQL JDBC Driver](https://jdbc.postgresql.org/documentation/head/index.html) documentation\. 

**Topics**
+ [Reducing DNS cache timeouts](#AuroraPostgreSQL.BestPractices.FastFailover.Configuring.Timeouts)
+ [Setting an Aurora PostgreSQL connection string for fast failover](#AuroraPostgreSQL.BestPractices.FastFailover.Configuring.ConnectionString)
+ [Other options for obtaining the host string](#AuroraPostgreSQL.BestPractices.FastFailover.Configuring.HostString)

### Reducing DNS cache timeouts<a name="AuroraPostgreSQL.BestPractices.FastFailover.Configuring.Timeouts"></a>

When your application tries to establish a connection after a failover, the new Aurora PostgreSQL writer will be a previous reader\. You can find it by using the Aurora read\-only endpoint before DNS updates have fully propagated\. Setting the java DNS time to live \(TTL\) to a low value helps cycle between reader nodes on later connection attempts\.

```
// Sets internal TTL to match the Aurora RO Endpoint TTL
java.security.Security.setProperty("networkaddress.cache.ttl" , "1");
// If the lookup fails, default to something like small to retry
java.security.Security.setProperty("networkaddress.cache.negative.ttl" , "3");
```

### Setting an Aurora PostgreSQL connection string for fast failover<a name="AuroraPostgreSQL.BestPractices.FastFailover.Configuring.ConnectionString"></a>

To use Aurora PostgreSQL fast failover, make sure that your application's connection string has a list of hosts instead of just a single host\. Following is an example connection string that you can use to connect to an Aurora PostgreSQL cluster\. In this example, the hosts are in bold\.

```
jdbc:postgresql://myauroracluster.cluster-c9bfei4hjlrd.us-east-1-beta.rds.amazonaws.com:5432,
myauroracluster.cluster-ro-c9bfei4hjlrd.us-east-1-beta.rds.amazonaws.com:5432
/postgres?user=<primaryuser>&password=<primarypw>&loginTimeout=2
&connectTimeout=2&cancelSignalTimeout=2&socketTimeout=60
&tcpKeepAlive=true&targetServerType=primary
```

For best availability and to avoid a dependency on the RDS API, we recommend that you maintain a file to connect with\. This file contains a host string that your application reads from when you establish a connection to the database\. This host string has all the Aurora endpoints available for the cluster\. For more information about Aurora endpoints, see [Amazon Aurora connection management](Aurora.Overview.Endpoints.md)\. 

For example, you might store your endpoints in a local file as shown following\.

```
myauroracluster.cluster-c9bfei4hjlrd.us-east-1-beta.rds.amazonaws.com:5432,
myauroracluster.cluster-ro-c9bfei4hjlrd.us-east-1-beta.rds.amazonaws.com:5432
```

Your application reads from this file to populate the host section of the JDBC connection string\. Renaming the DB cluster causes these endpoints to change\. Make sure that your application handles this event if it occurs\.

Another option is to use a list of DB instance nodes, as follows\.

```
my-node1.cksc6xlmwcyw.us-east-1-beta.rds.amazonaws.com:5432,
my-node2.cksc6xlmwcyw.us-east-1-beta.rds.amazonaws.com:5432,
my-node3.cksc6xlmwcyw.us-east-1-beta.rds.amazonaws.com:5432,
my-node4.cksc6xlmwcyw.us-east-1-beta.rds.amazonaws.com:5432
```

The benefit of this approach is that the PostgreSQL JDBC connection driver loops through all nodes on this list to find a valid connection\. In contrast, when you use the Aurora endpoints only two nodes are tried in each connection attempt\. However, there's a downside to using DB instance nodes\. If you add or remove nodes from your cluster and the list of instance endpoints becomes stale, the connection driver might never find the correct host to connect to\.

To help ensure that your application doesn't wait too long to connect to any one host, set the following parameters aggressively:
+ `targetServerType` – Controls whether the driver connects to a write or read node\. To ensure that your applications reconnect only to a write node, set the `targetServerType` value to `primary`\.

  Values for the `targetServerType` parameter include `primary`, `secondary`, `any`, and `preferSecondary`\. The `preferSecondary` value attempts to establish a connection to a reader first\. It connects to the writer if no reader connection can be established\.
+ `loginTimeout` – Controls how long your application waits to log in to the database after a socket connection has been established\.
+ `connectTimeout` – Controls how long the socket waits to establish a connection to the database\.

You can modify other application parameters to speed up the connection process, depending on how aggressive you want your application to be:
+ `cancelSignalTimeout` – In some applications, you might want to send a "best effort" cancel signal on a query that has timed out\. If this cancel signal is in your failover path, consider setting it aggressively to avoid sending this signal to a dead host\.
+ `socketTimeout` – This parameter controls how long the socket waits for read operations\. This parameter can be used as a global "query timeout" to ensure no query waits longer than this value\. A good practice is to have two connection handlers\. One connection handler runs short\-lived queries and sets this value lower\. Another connection handler, for long\-running queries, has this value set much higher\. With this approach, you can rely on TCP keepalive parameters to stop long\-running queries if the server goes down\.
+ `tcpKeepAlive` – Turn on this parameter to ensure the TCP keepalive parameters that you set are respected\.
+ `loadBalanceHosts` – When set to `true`, this parameter has the application connect to a random host chosen from a list of candidate hosts\.

### Other options for obtaining the host string<a name="AuroraPostgreSQL.BestPractices.FastFailover.Configuring.HostString"></a>

You can get the host string from several sources, including the `aurora_replica_status` function and by using the Amazon RDS API\.

In many cases, you need to determine who the writer of the cluster is or to find other reader nodes in the cluster\. To do this, your application can connect to any DB instance in the DB cluster and query the `aurora_replica_status` function\. You can use this function to reduce the amount of time it takes to find a host to connect to\. However, in certain network failure scenarios the `aurora_replica_status` function might show out\-of\-date or incomplete information\.

A good way to ensure that your application can find a node to connect to is to try to connect to the cluster writer endpoint and then the cluster reader endpoint\. You do this until you can establish a readable connection\. These endpoints don't change unless you rename your DB cluster\. Thus, you can generally leave them as static members of your application or store them in a resource file that your application reads from\.

After you establish a connection using one of these endpoints, you can get information about the rest of the cluster\. To do this, call the `aurora_replica_status` function\. For example, the following command retrieves information with `aurora_replica_status`\.

```
postgres=> SELECT server_id, session_id, highest_lsn_rcvd, cur_replay_latency_in_usec, now(), last_update_timestamp
FROM aurora_replica_status();

server_id | session_id | highest_lsn_rcvd | cur_replay_latency_in_usec | now | last_update_timestamp
-----------+--------------------------------------+------------------+----------------------------+-------------------------------+------------------------
mynode-1 | 3e3c5044-02e2-11e7-b70d-95172646d6ca | 594221001 | 201421 | 2017-03-07 19:50:24.695322+00 | 2017-03-07 19:50:23+00
mynode-2 | 1efdd188-02e4-11e7-becd-f12d7c88a28a | 594221001 | 201350 | 2017-03-07 19:50:24.695322+00 | 2017-03-07 19:50:23+00
mynode-3 | MASTER_SESSION_ID | | | 2017-03-07 19:50:24.695322+00 | 2017-03-07 19:50:23+00
(3 rows)
```

For example, the hosts section of your connection string might start with both the writer and reader cluster endpoints, as shown following\.

```
myauroracluster.cluster-c9bfei4hjlrd.us-east-1-beta.rds.amazonaws.com:5432,
myauroracluster.cluster-ro-c9bfei4hjlrd.us-east-1-beta.rds.amazonaws.com:5432
```

In this scenario, your application attempts to establish a connection to any node type, primary or secondary\. When your application is connected, a good practice is to first examine the read/write status of the node\. To do this, query for the result of the command `SHOW transaction_read_only`\.

If the return value of the query is `OFF`, then you successfully connected to the primary node\. However, suppose that the return value is `ON` and your application requires a read/write connection\. In this case, you can call the `aurora_replica_status` function to determine the `server_id` that has `session_id='MASTER_SESSION_ID'`\. This function gives you the name of the primary node\. You can use this with the `endpointPostfix` described following\.

Make sure that you're aware when you connect to a replica that has stale data\. When this happens, the `aurora_replica_status` function might show out\-of\-date information\. You can set a threshold for staleness at the application level\. To check this, you can look at the difference between the server time and the `last_update_timestamp` value\. In general, your application should avoid flipping between two hosts due to conflicting information returned by the `aurora_replica_status` function\. Your application should try all known hosts first instead of following the data returned by `aurora_replica_status`\.

#### Listing instances using the DescribeDBClusters API operation, example in Java<a name="AuroraPostgreSQL.BestPractices.FastFailover.Configuring.HostString.API"></a>

You can programmatically find the list of instances by using the [AWS SDK for Java](https://aws.amazon.com/sdk-for-java/), specifically the [DescribeDBClusters](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBClusters.html) API operation\. 

Following is a small example of how you might do this in Java 8\.

```
AmazonRDS client = AmazonRDSClientBuilder.defaultClient();
DescribeDBClustersRequest request = new DescribeDBClustersRequest()
   .withDBClusterIdentifier(clusterName);
DescribeDBClustersResult result = 
rdsClient.describeDBClusters(request);

DBCluster singleClusterResult = result.getDBClusters().get(0);

String pgJDBCEndpointStr = 
singleClusterResult.getDBClusterMembers().stream()
   .sorted(Comparator.comparing(DBClusterMember::getIsClusterWriter)
   .reversed()) // This puts the writer at the front of the list
   .map(m -> m.getDBInstanceIdentifier() + endpointPostfix + ":" + singleClusterResult.getPort()))
   .collect(Collectors.joining(","));
```

Here, `pgJDBCEndpointStr` contains a formatted list of endpoints, as shown following\.

```
my-node1.cksc6xlmwcyw.us-east-1-beta.rds.amazonaws.com:5432,
my-node2.cksc6xlmwcyw.us-east-1-beta.rds.amazonaws.com:5432
```

The variable `endpointPostfix` can be a constant that your application sets\. Or your application can get it by querying the `DescribeDBInstances` API operation for a single instance in your cluster\. This value remains constant within an AWS Region and for an individual customer\. So it saves an API call to simply keep this constant in a resource file that your application reads from\. In the example preceding, it's set to the following\.

```
.cksc6xlmwcyw.us-east-1-beta.rds.amazonaws.com
```

For availability purposes, a good practice is to default to using the Aurora endpoints of your DB cluster if the API isn't responding or takes too long to respond\. The endpoints are guaranteed to be up to date within the time it takes to update the DNS record\. Updating the DNS record with an endpoint typically takes less than 30 seconds\. You can store the endpoint in a resource file that your application consumes\. 

## Testing failover<a name="AuroraPostgreSQL.BestPracticesFastFailover.Testing"></a>

In all cases you must have a DB cluster with two or more DB instances in it\.

From the server side, certain API operations can cause an outage that can be used to test how your applications responds:
+ [FailoverDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_FailoverDBCluster.html) – This operation attempts to promote a new DB instance in your DB cluster to writer\.

  The following code example shows how you can use `failoverDBCluster` to cause an outage\. For more details about setting up an Amazon RDS client, see [Using the AWS SDK for Java](https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/basics.html)\. 

  ```
  public void causeFailover() {
      
      final AmazonRDS rdsClient = AmazonRDSClientBuilder.defaultClient();
     
      FailoverDBClusterRequest request = new FailoverDBClusterRequest();
      request.setDBClusterIdentifier("cluster-identifier");
  
      rdsClient.failoverDBCluster(request);
  }
  ```
+ [RebootDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_RebootDBInstance.html) – Failover isn't guaranteed with this API operation\. It shuts down the database on the writer, however\. You can use it to test how your application responds to connections dropping\. The `ForceFailover` parameter doesn't apply for Aurora engines\. Instead, use the `FailoverDBCluster` API operation\.
+ [ModifyDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBCluster.html) – Modifying the `Port` parameter causes an outage when the nodes in the cluster begin listening on a new port\. In general, your application can respond to this failure first by ensuring that only your application controls port changes\. Also, ensure that it can appropriately update the endpoints it depends on\. You can do this by having someone manually update the port when they make modifications at the API level\. Or you can do this by using the RDS API in your application to determine if the port has changed\.
+ [ModifyDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBInstance.html) – Modifying the `DBInstanceClass` parameter causes an outage\.
+ [DeleteDBInstance](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteDBInstance.html) – Deleting the primary \(writer\) causes a new DB instance to be promoted to writer in your DB cluster\.

From the application or client side, if you use Linux, you can test how the application responds to sudden packet drops\. You can do this based on whether port, host, or if TCP keepalive packets are sent or received by using the iptables command\. 

## Fast failover example in Java<a name="AuroraPostgreSQL.BestPractices.FastFailover.Example"></a>

The following code example shows how an application might set up an Aurora PostgreSQL driver manager\. 

The application calls the `getConnection` function when it needs a connection\. A call to `getConnection` can fail to find a valid host\. An example is when no writer is found but the `targetServerType` parameter is set to `primary`\. In this case, the calling application should simply retry calling the function\. 

To avoid pushing the retry behavior onto the application, you can wrap this retry call into a connection pooler\. With most connection poolers, you can specify a JDBC connection string\. So your application can call into `getJdbcConnectionString` and pass that to the connection pooler\. Doing this means you can use faster failover with Aurora PostgreSQL\.

```
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.ArrayList;
import java.util.List;
import java.util.stream.Collectors;
import java.util.stream.IntStream;

import org.joda.time.Duration;

public class FastFailoverDriverManager {
   private static Duration LOGIN_TIMEOUT = Duration.standardSeconds(2);
   private static Duration CONNECT_TIMEOUT = Duration.standardSeconds(2);
   private static Duration CANCEL_SIGNAL_TIMEOUT = Duration.standardSeconds(1);
   private static Duration DEFAULT_SOCKET_TIMEOUT = Duration.standardSeconds(5);

   public FastFailoverDriverManager() {
       try {
            Class.forName("org.postgresql.Driver");
       } catch (ClassNotFoundException e) {
            e.printStackTrace();
       }

       /*
         * RO endpoint has a TTL of 1s, we should honor that here. Setting this aggressively makes sure that when
         * the PG JDBC driver creates a new connection, it will resolve a new different RO endpoint on subsequent attempts
         * (assuming there is > 1 read node in your cluster)
         */
        java.security.Security.setProperty("networkaddress.cache.ttl" , "1");
       // If the lookup fails, default to something like small to retry
       java.security.Security.setProperty("networkaddress.cache.negative.ttl" , "3");
   }

   public Connection getConnection(String targetServerType) throws SQLException {
       return getConnection(targetServerType, DEFAULT_SOCKET_TIMEOUT);
   }

   public Connection getConnection(String targetServerType, Duration queryTimeout) throws SQLException {
        Connection conn = DriverManager.getConnection(getJdbcConnectionString(targetServerType, queryTimeout));

       /*
         * A good practice is to set socket and statement timeout to be the same thing since both 
         * the client AND server will stop the query at the same time, leaving no running queries 
         * on the backend
         */
        Statement st = conn.createStatement();
        st.execute("set statement_timeout to " + queryTimeout.getMillis());
        st.close();

       return conn;
   }

   private static String urlFormat = "jdbc:postgresql://%s"
           + "/postgres"
           + "?user=%s"
           + "&password=%s"
           + "&loginTimeout=%d"
           + "&connectTimeout=%d"
           + "&cancelSignalTimeout=%d"
           + "&socketTimeout=%d"
           + "&targetServerType=%s"
           + "&tcpKeepAlive=true"
           + "&ssl=true"
           + "&loadBalanceHosts=true";
   public String getJdbcConnectionString(String targetServerType, Duration queryTimeout) {
       return String.format(urlFormat, 
                getFormattedEndpointList(getLocalEndpointList()),
                CredentialManager.getUsername(),
                CredentialManager.getPassword(),
                LOGIN_TIMEOUT.getStandardSeconds(),
                CONNECT_TIMEOUT.getStandardSeconds(),
                CANCEL_SIGNAL_TIMEOUT.getStandardSeconds(),
                queryTimeout.getStandardSeconds(),
                targetServerType
       );
   }

   private List<String> getLocalEndpointList() {
       /*
         * As mentioned in the best practices doc, a good idea is to read a local resource file and parse the cluster endpoints. 
         * For illustration purposes, the endpoint list is hardcoded here
         */
        List<String> newEndpointList = new ArrayList<>();
        newEndpointList.add("myauroracluster.cluster-c9bfei4hjlrd.us-east-1-beta.rds.amazonaws.com:5432");
        newEndpointList.add("myauroracluster.cluster-ro-c9bfei4hjlrd.us-east-1-beta.rds.amazonaws.com:5432");

       return newEndpointList;
   }

   private static String getFormattedEndpointList(List<String> endpoints) {
       return IntStream.range(0, endpoints.size())
               .mapToObj(i -> endpoints.get(i).toString())
               .collect(Collectors.joining(","));
   }
}
```