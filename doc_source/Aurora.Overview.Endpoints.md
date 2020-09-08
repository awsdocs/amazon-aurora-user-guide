# Amazon Aurora Connection Management<a name="Aurora.Overview.Endpoints"></a>

 Amazon Aurora typically involves a cluster of DB instances instead of a single instance\. Each connection is handled by a specific DB instance\. When you connect to an Aurora cluster, the host name and port that you specify point to an intermediate handler called an *endpoint*\. Aurora uses the endpoint mechanism to abstract these connections\. Thus, you don't have to hardcode all the hostnames or write your own logic for load\-balancing and rerouting connections when some DB instances aren't available\. 

 For certain Aurora tasks, different instances or groups of instances perform different roles\. For example, the primary instance handles all data definition language \(DDL\) and data manipulation language \(DML\) statements\. Up to 15 Aurora Replicas handle read\-only query traffic\. 

 Using endpoints, you can map each connection to the appropriate instance or group of instances based on your use case\. For example, to perform DDL statements you can connect to whichever instance is the primary instance\. To perform queries, you can connect to the reader endpoint, with Aurora automatically performing load\-balancing among all the Aurora Replicas\. For clusters with DB instances of different capacities or configurations, you can connect to custom endpoints associated with different subsets of DB instances\. For diagnosis or tuning, you can connect to a specific instance endpoint to examine details about a specific DB instance\. 

**Topics**
+ [Types of Aurora Endpoints](#Aurora.Overview.Endpoints.Types)
+ [Viewing the Endpoints for an Aurora Cluster](#Aurora.Endpoints.Viewing)
+ [Using the Cluster Endpoint](#Aurora.Endpoints.Cluster)
+ [Using the Reader Endpoint](#Aurora.Endpoints.Reader)
+ [Using Custom Endpoints](#Aurora.Endpoints.Custom)
+ [Creating a Custom Endpoint](#aurora-custom-endpoint-creating)
+ [Viewing Custom Endpoints](#aurora-endpoint-viewing)
+ [Editing a Custom Endpoint](#aurora-endpoint-editing)
+ [Deleting a Custom Endpoint](#aurora-endpoints-custom-deleting)
+ [End\-to\-End AWS CLI Example for Custom Endpoints](#Aurora.Endpoint.Tutorial)
+ [Using the Instance Endpoints](#Aurora.Endpoints.Instance)
+ [How Aurora Endpoints Work with High Availability](#Aurora.Overview.Endpoints.HA)

## Types of Aurora Endpoints<a name="Aurora.Overview.Endpoints.Types"></a>

 An endpoint is represented as an Aurora\-specific URL that contains a host address and a port\. The following types of endpoints are available from an Aurora DB cluster\. 

**Cluster endpoint**  
 A *cluster endpoint* \(or *writer endpoint*\) for an Aurora DB cluster connects to the current primary DB instance for that DB cluster\. This endpoint is the only one that can perform write operations such as DDL statements\. Because of this, the cluster endpoint is the one that you connect to when you first set up a cluster or when your cluster only contains a single DB instance\.   
 Each Aurora DB cluster has one cluster endpoint and one primary DB instance\.   
 You use the cluster endpoint for all write operations on the DB cluster, including inserts, updates, deletes, and DDL changes\. You can also use the cluster endpoint for read operations, such as queries\.   
 The cluster endpoint provides failover support for read/write connections to the DB cluster\. If the current primary DB instance of a DB cluster fails, Aurora automatically fails over to a new primary DB instance\. During a failover, the DB cluster continues to serve connection requests to the cluster endpoint from the new primary DB instance, with minimal interruption of service\.   
 The following example illustrates a cluster endpoint for an Aurora MySQL DB cluster\.   
 `mydbcluster.cluster-123456789012.us-east-1.rds.amazonaws.com:3306` 

**Reader endpoint**  
 A *reader endpoint* for an Aurora DB cluster provides load\-balancing support for read\-only connections to the DB cluster\. Use the reader endpoint for read operations, such as queries\. By processing those statements on the read\-only Aurora Replicas, this endpoint reduces the overhead on the primary instance\. It also helps the cluster to scale the capacity to handle simultaneous `SELECT` queries, proportional to the number of Aurora Replicas in the cluster\. Each Aurora DB cluster has one reader endpoint\.   
 If the cluster contains one or more Aurora Replicas, the reader endpoint load\-balances each connection request among the Aurora Replicas\. In that case, you can only perform read\-only statements such as `SELECT` in that session\. If the cluster only contains a primary instance and no Aurora Replicas, the reader endpoint connects to the primary instance\. In that case, you can perform write operations through the endpoint\.   
 The following example illustrates a reader endpoint for an Aurora MySQL DB cluster\.   
 `mydbcluster.cluster-ro-123456789012.us-east-1.rds.amazonaws.com:3306` 

**Custom endpoint**  
 A *custom endpoint* for an Aurora cluster represents a set of DB instances that you choose\. When you connect to the endpoint, Aurora performs load balancing and chooses one of the instances in the group to handle the connection\. You define which instances this endpoint refers to, and you decide what purpose the endpoint serves\.   
 An Aurora DB cluster has no custom endpoints until you create one\. You can create up to five custom endpoints for each provisioned Aurora cluster\. You can't use custom endpoints for Aurora Serverless clusters\.   
 The custom endpoint provides load\-balanced database connections based on criteria other than the read\-only or read/write capability of the DB instances\. For example, you might define a custom endpoint to connect to instances that use a particular AWS instance class or a particular DB parameter group\. Then you might tell particular groups of users about this custom endpoint\. For example, you might direct internal users to low\-capacity instances for report generation or ad hoc \(one\-time\) querying, and direct production traffic to high\-capacity instances\.   
 Because the connection can go to any DB instance that is associated with the custom endpoint, we recommend that you make sure that all the DB instances within that group share some similar characteristic\. Doing so ensures that the performance, memory capacity, and so on, are consistent for everyone who connects to that endpoint\.   
 This feature is intended for advanced users with specialized kinds of workloads where it isn't practical to keep all the Aurora Replicas in the cluster identical\. With custom endpoints, you can predict the capacity of the DB instance used for each connection\. When you use custom endpoints, you typically don't use the reader endpoint for that cluster\.   
 The following example illustrates a custom endpoint for a DB instance in an Aurora MySQL DB cluster\.   
 `myendpoint.cluster-custom-123456789012.us-east-1.rds.amazonaws.com:3306` 

**Instance endpoint**  
 An *instance endpoint* connects to a specific DB instance within an Aurora cluster\. Each DB instance in a DB cluster has its own unique instance endpoint\. So there is one instance endpoint for the current primary DB instance of the DB cluster, and there is one instance endpoint for each of the Aurora Replicas in the DB cluster\.   
 The instance endpoint provides direct control over connections to the DB cluster, for scenarios where using the cluster endpoint or reader endpoint might not be appropriate\. For example, your client application might require more fine\-grained load balancing based on workload type\. In this case, you can configure multiple clients to connect to different Aurora Replicas in a DB cluster to distribute read workloads\. For an example that uses instance endpoints to improve connection speed after a failover for Aurora PostgreSQL, see [Fast Failover with Amazon Aurora PostgreSQL](AuroraPostgreSQL.BestPractices.md#AuroraPostgreSQL.BestPractices.FastFailover)\. For an example that uses instance endpoints to improve connection speed after a failover for Aurora MySQL, see [MariaDB Connector/J failover support \- case Amazon Aurora](https://mariadb.org/mariadb-connectorj-failover-support-case-amazon-aurora/)\.   
 The following example illustrates an instance endpoint for a DB instance in an Aurora MySQL DB cluster\.   
 `mydbinstance.123456789012.us-east-1.rds.amazonaws.com:3306` 

## Viewing the Endpoints for an Aurora Cluster<a name="Aurora.Endpoints.Viewing"></a>

 In the AWS Management Console, you see the cluster endpoint, the reader endpoint, and any custom endpoints in the detail page for each cluster\. You see the instance endpoint in the detail page for each instance\. When you connect, you must append the associated port number, following a colon, to the endpoint name shown on this detail page\. 

 With the AWS CLI, you see the writer, reader, and any custom endpoints in the output of the [describe\-db\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html) command\. For example, the following command shows the endpoint attributes for all clusters in your current AWS Region\. 

```
aws rds describe-db-clusters --query '*[].{Endpoint:Endpoint,ReaderEndpoint:ReaderEndpoint,CustomEndpoints:CustomEndpoints}'
```

 With the Amazon RDS API, you retrieve the endpoints by calling the [DescribeDbClusterEndpoints](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDbClusterEndpoints.html) function\. 

## Using the Cluster Endpoint<a name="Aurora.Endpoints.Cluster"></a>

 Because each Aurora cluster has a single built\-in cluster endpoint, whose name and other attributes are managed by Aurora, you can't create, delete, or modify this kind of endpoint\. 

 You use the cluster endpoint when you administer your cluster, perform extract, transform, load \(ETL\) operations, or develop and test applications\. The cluster endpoint connects to the primary instance of the cluster\. The primary instance is the only DB instance where you can create tables and indexes, run `INSERT` statements, and perform other DDL and DML operations\. 

 The physical IP address pointed to by the cluster endpoint changes when the failover mechanism promotes a new DB instance to be the read/write primary instance for the cluster\. If you use any form of connection pooling or other multiplexing, be prepared to flush or reduce the time\-to\-live for any cached DNS information\. Doing so ensures that you don't try to establish a read/write connection to a DB instance that became unavailable or is now read\-only after a failover\. 

## Using the Reader Endpoint<a name="Aurora.Endpoints.Reader"></a>

 You use the reader endpoint for read\-only connections for your Aurora cluster\. This endpoint uses a load\-balancing mechanism to help your cluster handle a query\-intensive workload\. The reader endpoint is the endpoint that you supply to applications that do reporting or other read\-only operations on the cluster\. 

 The reader endpoint load\-balances connections to available Aurora Replicas in an Aurora DB cluster\. It doesn't load\-balance individual queries\. If you want to load\-balance each query to distribute the read workload for a DB cluster, open a new connection to the reader endpoint for each query\. 

 Each Aurora cluster has a single built\-in reader endpoint, whose name and other attributes are managed by Aurora\. You can't create, delete, or modify this kind of endpoint\. 

 If your cluster contains only a primary instance and no Aurora Replicas, the reader endpoint connects to the primary instance\. In that case, you can perform write operations through this endpoint\. 

## Using Custom Endpoints<a name="Aurora.Endpoints.Custom"></a>

 You use custom endpoints to simplify connection management when your cluster contains DB instances with different capacities and configuration settings\. 

 Previously, you might have used the CNAMES mechanism to set up Domain Name Service \(DNS\) aliases from your own domain to achieve similar results\. By using custom endpoints, you can avoid updating CNAME records when your cluster grows or shrinks\. Custom endpoints also mean that you can use encrypted Transport Layer Security/Secure Sockets Layer \(TLS/SSL\) connections\. 

 Instead of using one DB instance for each specialized purpose and connecting to its instance endpoint, you can have multiple groups of specialized DB instances\. In this case, each group has its own custom endpoint\. This way, Aurora can perform load balancing among all the instances dedicated to tasks such as reporting or handling production or internal queries\. The custom endpoints provide load balancing and high availability for each group of DB instances within your cluster\. If one of the DB instances within a group becomes unavailable, Aurora directs subsequent custom endpoint connections to one of the other DB instances associated with the same endpoint\. 

**Topics**
+ [Specifying Properties for Custom Endpoints](#Aurora.Endpoints.Custom.Properties)
+ [Membership Rules for Custom Endpoints](#Aurora.Endpoints.Custom.Membership)
+ [Managing Custom Endpoints](#Aurora.Endpoints.Custom.Managing)

### Specifying Properties for Custom Endpoints<a name="Aurora.Endpoints.Custom.Properties"></a>

 The maximum length for a custom endpoint name is 63 characters\. You can see the name format following: 

 `endpointName.cluster-custom-customerDnsIdentifier.dnsSuffix` 

 Because custom endpoint names don't include the name of your cluster, you don't have to change those names if you rename a cluster\. You can't reuse the same custom endpoint name for more than one cluster in the same region\. Give each custom endpoint a name that is unique across the clusters owned by your user ID within a particular region\. 

 Each custom endpoint has an associated type that determines which DB instances are eligible to be associated with that endpoint\. Currently, the type can be `READER`, `WRITER`, or `ANY`\. The following considerations apply to the custom endpoint types: 
+  Only DB instances that are read\-only Aurora Replicas can be part of a `READER` custom endpoint\. The `READER` type applies only to clusters using single\-master replication, because those clusters can include multiple read\-only DB instances\. 
+  Both read\-only Aurora Replicas and the read/write primary instance can be part of an `ANY` custom endpoint\. Aurora directs connections to cluster endpoints with type `ANY` to any associated DB instance with equal probability\. Because you can't determine in advance if you are connecting to the primary instance of a read\-only Aurora Replica, use this kind of endpoint for read\-only connections only\. The `ANY` type applies to clusters using any replication topology\. 
+  The `WRITER` type applies only to multi\-master clusters, because those clusters can include multiple read/write DB instances\. 
+  If you try to create a custom endpoint with a type that isn't appropriate based on the replication configuration for a cluster, Aurora returns an error\. 

### Membership Rules for Custom Endpoints<a name="Aurora.Endpoints.Custom.Membership"></a>

 When you add a DB instance to a custom endpoint or remove it from a custom endpoint, any existing connections to that DB instance remain active\. 

 You can define a list of DB instances to include in, or exclude from, a custom endpoint\. We refer to these lists as *static* and *exclusion* lists, respectively\. You can use the inclusion/exclusion mechanism to further subdivide the groups of DB instances, and to make sure that the set of custom endpoints covers all the DB instances in the cluster\. Each custom endpoint can contain only one of these list types\. 

 In the AWS Management Console, the choice is represented by the check box **Attach future instances added to this cluster**\. When you keep check box clear, the custom endpoint uses a static list containing only the DB instances specified in the dialog\. When you choose the check box, the custom endpoint uses an exclusion list\. In this case, the custom endpoint represents all DB instances in the cluster \(including any that you add in the future\) except the ones left unselected in the dialog\. The AWS CLI and Amazon RDS API have parameters representing each kind of list\. When you use the AWS CLI or Amazon RDS API, you can't add or remove individual members to the lists; you always specify the entire new list\. 

 Aurora doesn't change the DB instances specified in the static or exclusion lists when DB instances change roles between primary instance and Aurora Replica due to failover or promotion\. For example, a custom endpoint with type `READER` might include a DB instance that was an Aurora Replica and then was promoted to a primary instance\. The type of a custom endpoint \(`READER`, `WRITER`, or `ANY`\) determines what kinds of operations you can perform through that endpoint\. 

 You can associate a DB instance with more than one custom endpoint\. For example, suppose that you add a new DB instance to a cluster, or that Aurora adds a DB instance automatically through the autoscaling mechanism\. In these cases, the DB instance is added to all custom endpoints for which it is eligible\. Which endpoints the DB instance is added to is based on the custom endpoint type of `READER` , `WRITER`, or `ANY`, plus any static or exclusion lists defined for each endpoint\. For example, if the endpoint includes a static list of DB instances, newly added Aurora Replicas aren't added to that endpoint\. Conversely, if the endpoint has an exclusion list, newly added Aurora Replicas are added to the endpoint, if they aren't named in the exclusion list and their roles match the type of the custom endpoint\. 

 If an Aurora Replica becomes unavailable, it remains associated with any custom endpoints\. For example, it remains part of the custom endpoint when it is unhealthy, stopped, rebooting, and so on\. However, you can't connect to it through those endpoints until it becomes available again\. 

### Managing Custom Endpoints<a name="Aurora.Endpoints.Custom.Managing"></a>

 Because newly created Aurora clusters have no custom endpoints, you must create and manage these objects yourself\. You do so using the AWS Management Console, AWS CLI, or Amazon RDS API\. 

**Note**  
 You must also create and manage custom endpoints for Aurora clusters restored from snapshots\. Custom endpoints are not included in the snapshot\. You create them again after restoring, and choose new endpoint names if the restored cluster is in the same region as the original one\. 

 To work with custom endpoints from the AWS Management Console, you navigate to the details page for your Aurora cluster and use the controls under the **Custom Endpoints** section\. 

 To work with custom endpoints from the AWS CLI, you can use these operations: 
+  [create\-db\-cluster\-endpoint](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster-endpoint.html) 
+  [describe\-db\-cluster\-endpoints](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-cluster-endpoints.html) 
+  [modify\-db\-cluster\-endpoint](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster-endpoint.html) 
+  [delete\-db\-cluster\-endpoint](https://docs.aws.amazon.com/cli/latest/reference/rds/delete-db-cluster-endpoint.html) 

 To work with custom endpoints through the Amazon RDS API, you can use the following functions: 
+  [CreateDBClusterEndpoint](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBClusterEndpoint.html) 
+  [DescribeDBClusterEndpoints](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBClusterEndpoints.html) 
+  [ModifyDBClusterEndpoint](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBClusterEndpoint.html) 
+  [DeleteDBClusterEndpoint](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteDBClusterEndpoint.html) 

## Creating a Custom Endpoint<a name="aurora-custom-endpoint-creating"></a>

### Console<a name="aurora-create-endpoint.console"></a>

 To create a custom endpoint with the AWS Management Console, go to the cluster detail page and choose the `Create custom endpoint` action in the **Endpoints** section\. Choose a name for the custom endpoint, unique for your user ID and region\. To choose a list of DB instances that remains the same even as the cluster expands, keep the check box **Attach future instances added to this cluster** clear\. When you choose that check box, the custom endpoint dynamically adds any new instances as you add them to the cluster\. 

![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraCreateCustomEndpoint.png)

 You can't select the custom endpoint type of `ANY` or `READER` in the AWS Management Console\. All the custom endpoints you create through the AWS Management Console have a type of `ANY`\. 

### AWS CLI<a name="aurora-create-endpoint.cli"></a>

 To create a custom endpoint with the AWS CLI, run the [create\-db\-cluster\-endpoint](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster-endpoint.html) command\. 

 The following command creates a custom endpoint attached to a specific cluster\. Initially, the endpoint is associated with all the Aurora Replica instances in the cluster\. A subsequent command associates it with a specific set of DB instances in the cluster\. 

For Linux, macOS, or Unix:

```
aws rds create-db-cluster-endpoint --db-cluster-endpoint-identifier custom-endpoint-doc-sample \
  --endpoint-type reader \
  --db-cluster-identifier cluster_id

aws rds modify-db-cluster-endpoint --db-cluster-endpoint-identifier custom-endpoint-doc-sample \
  --static-members instance_name_1 instance_name_2
```

For Windows:

```
aws rds create-db-cluster-endpoint --db-cluster-endpoint-identifier custom-endpoint-doc-sample ^
  --endpoint-type reader ^
  --db-cluster-identifier cluster_id

aws rds modify-db-cluster-endpoint --db-cluster-endpoint-identifier custom-endpoint-doc-sample ^
  --static-members instance_name_1 instance_name_2
```

### RDS API<a name="aurora-create-endpoint.api"></a>

 To create a custom endpoint with the RDS API, run the [CreateDBClusterEndpoint](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBClusterEndpoint.html) operation\. 

## Viewing Custom Endpoints<a name="aurora-endpoint-viewing"></a>

### Console<a name="aurora-view-endpoint.console"></a>

 To view custom endpoints with the AWS Management Console, go to the cluster detail page for the cluster and look under the **Endpoints** section\. This section contains information only about custom endpoints\. The details for the built\-in endpoints are listed in the main **Details** section\. To see the details for a specific custom endpoint, select its name to bring up the detail page for that endpoint\. 

 The following screenshot shows how the list of custom endpoints for an Aurora cluster is initially empty\. 

![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraCustomEndpointEmptyList.png)

 After you create some custom endpoints for that cluster, they are shown under the **Endpoints** section\. 

![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraCustomEndpointList.png)

 Clicking through to the detail page shows which DB instances the endpoint is currently associated with\. 

![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraCustomEndpointDetail.png)

 To see the additional detail of whether new DB instances added to the cluster are automatically added to the endpoint also, bring up the **Edit** dialog for the endpoint\. 

### AWS CLI<a name="aurora-view-endpoint.cli"></a>

 To view custom endpoints with the AWS CLI, run the [describe\-db\-cluster\-endpoints](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-cluster-endpoints.html) command\. 

 The following command shows the custom endpoints associated with a specified cluster in a specified region\. The output includes both the built\-in endpoints and any custom endpoints\. 

For Linux, macOS, or Unix:

```
aws rds describe-db-cluster-endpoints --region region_name \
  --db-cluster-identifier cluster_id
```

For Windows:

```
aws rds describe-db-cluster-endpoints --region region_name ^
  --db-cluster-identifier cluster_id
```

 The following shows some sample output from a `describe-db-cluster-endpoints` command\. The `EndpointType` of `WRITER` or `READER` denotes the built\-in read/write and read\-only endpoints for the cluster\. The `EndpointType` of `CUSTOM` denotes endpoints that you create and choose the associated DB instances\. One of the endpoints has a non\-empty `StaticMembers` field, denoting that it is associated with a precise set of DB instances\. The other endpoint has a non\-empty `ExcludedMembers` field, denoting that the endpoint is associated with all DB instances *other than* the ones listed under `ExcludedMembers`\. This second kind of custom endpoint grows to accommodate new instances as you add them to the cluster\. 

```
{
	"DBClusterEndpoints": [
		{
			"Endpoint": "custom-endpoint-demo.cluster-123456789012.ca-central-1.rds.amazonaws.com",
			"Status": "available",
			"DBClusterIdentifier": "custom-endpoint-demo",
			"EndpointType": "WRITER"
		},
		{
			"Endpoint": "custom-endpoint-demo.cluster-ro-123456789012.ca-central-1.rds.amazonaws.com",
			"Status": "available",
			"DBClusterIdentifier": "custom-endpoint-demo",
			"EndpointType": "READER"
		},
		{
			"CustomEndpointType": "ANY",
			"DBClusterEndpointIdentifier": "powers-of-2",
			"ExcludedMembers": [],
			"DBClusterIdentifier": "custom-endpoint-demo",
			"Status": "available",
			"EndpointType": "CUSTOM",
			"Endpoint": "powers-of-2.cluster-custom-123456789012.ca-central-1.rds.amazonaws.com",
			"StaticMembers": [
					"custom-endpoint-demo-04",
					"custom-endpoint-demo-08",
					"custom-endpoint-demo-01",
					"custom-endpoint-demo-02"
			],
			"DBClusterEndpointResourceIdentifier": "cluster-endpoint-W7PE3TLLFNSHXQKFU6J6NV5FHU",
			"DBClusterEndpointArn": "arn:aws:rds:ca-central-1:111122223333:cluster-endpoint:powers-of-2"
		},
		{
			"CustomEndpointType": "ANY",
			"DBClusterEndpointIdentifier": "eight-and-higher",
			"ExcludedMembers": [
					"custom-endpoint-demo-04",
					"custom-endpoint-demo-02",
					"custom-endpoint-demo-07",
					"custom-endpoint-demo-05",
					"custom-endpoint-demo-03",
					"custom-endpoint-demo-06",
					"custom-endpoint-demo-01"
			],
			"DBClusterIdentifier": "custom-endpoint-demo",
			"Status": "available",
			"EndpointType": "CUSTOM",
			"Endpoint": "eight-and-higher.cluster-custom-123456789012.ca-central-1.rds.amazonaws.com",
			"StaticMembers": [],
			"DBClusterEndpointResourceIdentifier": "cluster-endpoint-W7PE3TLLFNSHYQKFU6J6NV5FHU",
			"DBClusterEndpointArn": "arn:aws:rds:ca-central-1:111122223333:cluster-endpoint:eight-and-higher"
		}
	]
}
```

### RDS API<a name="aurora-view-endpoint.api"></a>

 To view custom endpoints with the RDS API, run the [DescribeDBClusterEndpoints\.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBClusterEndpoints.html.html) operation\. 

## Editing a Custom Endpoint<a name="aurora-endpoint-editing"></a>

 You can edit the properties of a custom endpoint to change which DB instances are associated with the endpoint\. You can also change an endpoint between a static list and an exclusion list\. If you need more details about these endpoint properties, see [Membership Rules for Custom Endpoints](#Aurora.Endpoints.Custom.Membership)\. 

 You can't connect to or use a custom endpoint while the changes from an edit action are in progress\. It might take some minutes before the endpoint status returns to **Available** and you can connect again\. 

### Console<a name="aurora-edit-endpoint.console"></a>

 To edit a custom endpoint with the AWS Management Console, you can select the endpoint on the cluster detail page, or bring up the detail page for the endpoint, and choose the **Edit** action\. 

![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraEditCustomEndpoint.png)

### AWS CLI<a name="aurora-edit-endpoint.cli"></a>

 To edit a custom endpoint with the AWS CLI, run the [modify\-db\-cluster\-endpoint](https://docs.aws.amazon.com/cli/latest/reference/rds/modify-db-cluster-endpoint.html) command\. 

 The following commands change the set of DB instances that apply to a custom endpoint and optionally switches between the behavior of a static or exclusion list\. The `--static-members` and `--excluded-members` parameters take a space\-separated list of DB instance identifiers\. 

For Linux, macOS, or Unix:

```
aws rds modify-db-cluster-endpoint --db-cluster-endpoint-identifier my-custom-endpoint \
  --static-members db-instance-id-1 db-instance-id-2 db-instance-id-3 \
  --region region_name

aws rds modify-db-cluster-endpoint --db-cluster-endpoint-identifier my-custom-endpoint \
  --excluded-members db-instance-id-4 db-instance-id-5 \
  --region region_name
```

For Windows:

```
aws rds modify-db-cluster-endpoint --db-cluster-endpoint-identifier my-custom-endpoint ^
  --static-members db-instance-id-1 db-instance-id-2 db-instance-id-3 ^
  --region region_name

aws rds modify-db-cluster-endpoint --db-cluster-endpoint-identifier my-custom-endpoint ^
  --excluded-members db-instance-id-4 db-instance-id-5 ^
  --region region_name
```

### RDS API<a name="aurora-edit-endpoint.api"></a>

 To edit a custom endpoint with the RDS API, run the [ModifyDBClusterEndpoint\.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_ModifyDBClusterEndpoint.html.html) operation\. 

## Deleting a Custom Endpoint<a name="aurora-endpoints-custom-deleting"></a>

### Console<a name="aurora-delete-endpoint.console"></a>

 To delete a custom endpoint with the AWS Management Console, go to the cluster detail page, select the appropriate custom endpoint, and select the **Delete** action\. 

![\[\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/AuroraDeleteCustomEndpoint.png)

### AWS CLI<a name="aurora-delete-endpoint.cli"></a>

 To delete a custom endpoint with the AWS CLI, run the [delete\-db\-cluster\-endpoint](https://docs.aws.amazon.com/cli/latest/reference/rds/delete-db-cluster-endpoint.html) command\. 

 The following command deletes a custom endpoint\. You don't need to specify the associated cluster, but you must specify the region\. 

For Linux, macOS, or Unix:

```
aws rds delete-db-cluster-endpoint --db-cluster-endpoint-identifier custom-end-point-id \
  --region region_name
```

For Windows:

```
aws rds delete-db-cluster-endpoint --db-cluster-endpoint-identifier custom-end-point-id ^
  --region region_name
```

### RDS API<a name="aurora-delete-endpoint.api"></a>

 To delete a custom endpoint with the RDS API, run the [DeleteDBClusterEndpoint](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DeleteDBClusterEndpoint.html) operation\. 

## End\-to\-End AWS CLI Example for Custom Endpoints<a name="Aurora.Endpoint.Tutorial"></a>

 The following tutorial uses AWS CLI examples with Unix shell syntax to show you might define a cluster with several "small" DB instances and a few "big" DB instances, and create custom endpoints to connect to each set of DB instances\. To run similar commands on your own system, you should be familiar enough with the basics of working with Aurora clusters and AWS CLI usage to supply your own values for parameters such as region, subnet group, and VPC security group\. 

 This example demonstrates the initial setup steps: creating an Aurora cluster and adding DB instances to it\. This is a heterogeneous cluster, meaning not all the DB instances have the same capacity\. Most instances use the AWS instance class `db.r4.4xlarge`, but the last two DB instances use `db.r4.16xlarge`\. Each of these sample `create-db-instance` commands prints its output to the screen and saves a copy of the JSON in a file for later inspection\. 

```
aws rds create-db-cluster --db-cluster-identifier custom-endpoint-demo --engine aurora \
     --engine-version 5.6.10a --master-username $MASTER_USER --master-user-password $MASTER_PW \
     --db-subnet-group-name $SUBNET_GROUP  --vpc-security-group-ids $VPC_SECURITY_GROUP \
     --region $REGION

for i in 01 02 03 04 05 06 07 08
do
  aws rds create-db-instance --db-instance-identifier custom-endpoint-demo-${i} \
     --engine aurora --db-cluster-identifier custom-endpoint-demo --db-instance-class db.r4.4xlarge \
     --region $REGION \
     | tee custom-endpoint-demo-${i}.json
done

for i in 09 10
do
  aws rds create-db-instance --db-instance-identifier custom-endpoint-demo-${i} \
     --engine aurora --db-cluster-identifier custom-endpoint-demo --db-instance-class db.r4.16xlarge \
     --region $REGION \
     | tee custom-endpoint-demo-${i}.json
done
```

 The larger instances are reserved for specialized kinds of reporting queries\. To make it unlikely for them to be promoted to the primary instance, the following example changes their promotion tier to the lowest priority\. 

```
for i in 09 10
do
  aws rds modify-db-instance --db-instance-id custom-endpoint-demo-${i} \
    --region $REGION --promotion-tier 15
done
```

 Suppose you want to use the two "bigger" instances only for the most resource\-intensive queries\. You can create a custom read\-only endpoint, and then add a static list of members so that the endpoint connects only to those DB instances\. Those instances are already in the lowest promotion tier, making it unlikely either of them would ever be promoted to the primary instance\. If one of them was promoted to the primary instance, it would become unreachable through this endpoint because we specified the `READER` type instead of the `ANY` type\. The following example demonstrates the create and modify endpoint commands, and sample JSON output showing the initial and modified state of the custom endpoint\. 

```
$ aws rds create-db-cluster-endpoint --region $REGION \
    --db-cluster-identifier custom-endpoint-demo \
    --db-cluster-endpoint-identifier big-instances --endpoint-type reader
{
    "EndpointType": "CUSTOM",
    "Endpoint": "big-instances.cluster-custom-123456789012.ca-central-1.rds.amazonaws.com",
    "DBClusterEndpointIdentifier": "big-instances",
    "DBClusterIdentifier": "custom-endpoint-demo",
    "StaticMembers": [],
    "DBClusterEndpointResourceIdentifier": "cluster-endpoint-W7PE3TLLFNSHXQKFU6J6NV5FHU",
    "ExcludedMembers": [],
    "CustomEndpointType": "READER",
    "Status": "creating",
    "DBClusterEndpointArn": "arn:aws:rds:ca-central-1:111122223333:cluster-endpoint:big-instances"
}

$ aws rds modify-db-cluster-endpoint --db-cluster-endpoint-identifier big-instances \
    --static-members custom-endpoint-demo-09 custom-endpoint-demo-10 --region $REGION
{
    "EndpointType": "CUSTOM",
    "ExcludedMembers": [],
    "DBClusterEndpointIdentifier": "big-instances",
    "DBClusterEndpointResourceIdentifier": "cluster-endpoint-W7PE3TLLFNSHXQKFU6J6NV5FHU",
    "CustomEndpointType": "READER",
    "DBClusterEndpointArn": "arn:aws:rds:ca-central-1:111122223333:cluster-endpoint:big-instances",
    "StaticMembers": [
        "custom-endpoint-demo-10",
        "custom-endpoint-demo-09"
    ],
    "Status": "modifying",
    "Endpoint": "big-instances.cluster-custom-123456789012.ca-central-1.rds.amazonaws.com",
    "DBClusterIdentifier": "custom-endpoint-demo"
}
```

 The default `READER` endpoint for the cluster can connect to either the "small" or "big" DB instances, making it impractical to predict query performance and scalability when the cluster becomes busy\. To divide the workload cleanly between the sets of DB instances, you can ignore the default `READER` endpoint and create a second custom endpoint that connects to all other DB instances\. The following example does so by creating a custom endpoint and then adding an exclusion list\. Any other DB instances you add to the cluster later will be added to this endpoint automatically\. The `ANY` type means that this endpoint is associated with eight instances in total: the primary instance and another seven Aurora Replicas\. If the example used the `READER` type, the custom endpoint would only be associated with the seven Aurora Replicas\. 

```
$ aws rds create-db-cluster-endpoint --region $REGION --db-cluster-identifier custom-endpoint-demo \
    --db-cluster-endpoint-identifier small-instances --endpoint-type any
{
    "Status": "creating",
    "DBClusterEndpointIdentifier": "small-instances",
    "CustomEndpointType": "ANY",
    "EndpointType": "CUSTOM",
    "Endpoint": "small-instances.cluster-custom-123456789012.ca-central-1.rds.amazonaws.com",
    "StaticMembers": [],
    "ExcludedMembers": [],
    "DBClusterIdentifier": "custom-endpoint-demo",
    "DBClusterEndpointArn": "arn:aws:rds:ca-central-1:111122223333:cluster-endpoint:small-instances",
    "DBClusterEndpointResourceIdentifier": "cluster-endpoint-6RDDXQOC3AKKZT2PRD7ST37BMY"
}

$ aws rds modify-db-cluster-endpoint --db-cluster-endpoint-identifier small-instances \
    --excluded-members custom-endpoint-demo-09 custom-endpoint-demo-10 --region $REGION
{
    "DBClusterEndpointIdentifier": "small-instances",
    "DBClusterEndpointArn": "arn:aws:rds:ca-central-1:111122223333:cluster-endpoint:small-instances",
    "DBClusterEndpointResourceIdentifier": "cluster-endpoint-6RDDXQOC3AKKZT2PRD7ST37BMY",
    "CustomEndpointType": "ANY",
    "Endpoint": "small-instances.cluster-custom-123456789012.ca-central-1.rds.amazonaws.com",
    "EndpointType": "CUSTOM",
    "ExcludedMembers": [
        "custom-endpoint-demo-09",
        "custom-endpoint-demo-10"
    ],
    "StaticMembers": [],
    "DBClusterIdentifier": "custom-endpoint-demo",
    "Status": "modifying"
}
```

 The following example checks the state of the endpoints for this cluster\. The cluster still has its original cluster endpoint, with `EndPointType` of `WRITER`, which you would still use for administration, ETL, and other write operations\. It still has its original `READER` endpoint, which you wouldn't use because each connection to it might be directed to a "small" or "big" DB instance\. The custom endpoints make this behavior predictable, with connections guaranteed to use one of the "small" or "big" DB instances based on the endpoint you specify\. 

```
$ aws rds describe-db-cluster-endpoints --region $REGION
{
    "DBClusterEndpoints": [
        {
            "EndpointType": "WRITER",
            "Endpoint": "custom-endpoint-demo.cluster-123456789012.ca-central-1.rds.amazonaws.com",
            "Status": "available",
            "DBClusterIdentifier": "custom-endpoint-demo"
        },
        {
            "EndpointType": "READER",
            "Endpoint": "custom-endpoint-demo.cluster-ro-123456789012.ca-central-1.rds.amazonaws.com",
            "Status": "available",
            "DBClusterIdentifier": "custom-endpoint-demo"
        },
        {
            "Endpoint": "small-instances.cluster-custom-123456789012.ca-central-1.rds.amazonaws.com",
            "CustomEndpointType": "ANY",
            "DBClusterEndpointArn": "arn:aws:rds:ca-central-1:111122223333:cluster-endpoint:small-instances",
            "ExcludedMembers": [
                "custom-endpoint-demo-09",
                "custom-endpoint-demo-10"
            ],
            "DBClusterEndpointResourceIdentifier": "cluster-endpoint-6RDDXQOC3AKKZT2PRD7ST37BMY",
            "DBClusterIdentifier": "custom-endpoint-demo",
            "StaticMembers": [],
            "EndpointType": "CUSTOM",
            "DBClusterEndpointIdentifier": "small-instances",
            "Status": "modifying"
        },
        {
            "Endpoint": "big-instances.cluster-custom-123456789012.ca-central-1.rds.amazonaws.com",
            "CustomEndpointType": "READER",
            "DBClusterEndpointArn": "arn:aws:rds:ca-central-1:111122223333:cluster-endpoint:big-instances",
            "ExcludedMembers": [],
            "DBClusterEndpointResourceIdentifier": "cluster-endpoint-W7PE3TLLFNSHXQKFU6J6NV5FHU",
            "DBClusterIdentifier": "custom-endpoint-demo",
            "StaticMembers": [
                "custom-endpoint-demo-10",
                "custom-endpoint-demo-09"
            ],
            "EndpointType": "CUSTOM",
            "DBClusterEndpointIdentifier": "big-instances",
            "Status": "available"
        }
    ]
}
```

 The final examples demonstrate how successive database connections to the custom endpoints connect to the various DB instances in the Aurora cluster\. The `small-instances` endpoint always connects to the `db.r4.4xlarge` DB instances, which are the lower\-numbered hosts in this cluster\. 

```
$ mysql -h small-instances.cluster-custom-123456789012.ca-central-1.rds.amazonaws.com -u $MYUSER -p
mysql> select @@aurora_server_id;
+-------------------------+
| @@aurora_server_id      |
+-------------------------+
| custom-endpoint-demo-02 |
+-------------------------+

$ mysql -h small-instances.cluster-custom-123456789012.ca-central-1.rds.amazonaws.com -u $MYUSER -p
mysql> select @@aurora_server_id;
+-------------------------+
| @@aurora_server_id      |
+-------------------------+
| custom-endpoint-demo-07 |
+-------------------------+

$ mysql -h small-instances.cluster-custom-123456789012.ca-central-1.rds.amazonaws.com -u $MYUSER -p
mysql> select @@aurora_server_id;
+-------------------------+
| @@aurora_server_id      |
+-------------------------+
| custom-endpoint-demo-01 |
+-------------------------+
```

 The `big-instances` endpoint always connects to the `db.r4.16xlarge` DB instances, which are the two highest\-numbered hosts in this cluster\. 

```
$ mysql -h big-instances.cluster-custom-123456789012.ca-central-1.rds.amazonaws.com -u $MYUSER -p
mysql> select @@aurora_server_id;
+-------------------------+
| @@aurora_server_id      |
+-------------------------+
| custom-endpoint-demo-10 |
+-------------------------+

$ mysql -h big-instances.cluster-custom-123456789012.ca-central-1.rds.amazonaws.com -u $MYUSER -p
mysql> select @@aurora_server_id;
+-------------------------+
| @@aurora_server_id      |
+-------------------------+
| custom-endpoint-demo-09 |
+-------------------------+
```

## Using the Instance Endpoints<a name="Aurora.Endpoints.Instance"></a>

 Each DB instance in an Aurora cluster has its own built\-in instance endpoint, whose name and other attributes are managed by Aurora\. You can't create, delete, or modify this kind of endpoint\. You might be familiar with instance endpoints if you use Amazon RDS\. However, with Aurora you typically use the writer and reader endpoints more often than the instance endpoints\. 

 In day\-to\-day Aurora operations, the main way that you use instance endpoints is to diagnose capacity or performance issues that affect one specific instance in an Aurora cluster\. While connected to a specific instance, you can examine its status variables, metrics, and so on\. Doing this can help you determine what's happening for that instance that's different from what's happening for other instances in the cluster\. 

 In advanced use cases, you might configure some DB instances differently than others\. In this case, use the instance endpoint to connect directly to an instance that is smaller, larger, or otherwise has different characteristics than the others\. Also, set up failover priority so that this special DB instance is the last choice to take over as the primary instance\. We recommend that you use custom endpoints instead of the instance endpoint in such cases\. Doing so simplifies connection management and high availability as you add more DB instances to your cluster\. 

## How Aurora Endpoints Work with High Availability<a name="Aurora.Overview.Endpoints.HA"></a>

 For clusters where high availability is important, use the writer endpoint for read/write or general\-purpose connections and the reader endpoint for read\-only connections\. The writer and reader endpoints manage DB instance failover better than instance endpoints do\. Unlike the instance endpoints, the writer and reader endpoints automatically change which DB instance they connect to if a DB instance in your cluster becomes unavailable\. 

 If the primary DB instance of a DB cluster fails, Aurora automatically fails over to a new primary DB instance\. It does so by either promoting an existing Aurora Replica to a new primary DB instance or creating a new primary DB instance\. If a failover occurs, you can use the writer endpoint to reconnect to the newly promoted or created primary DB instance, or use the reader endpoint to reconnect to one of the Aurora Replicas in the DB cluster\. During a failover, the reader endpoint might direct connections to the new primary DB instance of a DB cluster for a short time after an Aurora Replica is promoted to the new primary DB instance\. 

 If you design your own application logic to manage connections to instance endpoints, you can manually or programmatically discover the resulting set of available DB instances in the DB cluster\. You can then confirm their instance classes after failover and connect to an appropriate instance endpoint\. 

 For more information about failovers, see [Fault Tolerance for an Aurora DB Cluster](Aurora.Managing.Backups.md#Aurora.Managing.FaultTolerance)\. 