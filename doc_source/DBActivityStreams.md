# Monitoring Amazon Aurora using Database Activity Streams<a name="DBActivityStreams"></a>

The Database Activity Streams feature provides a near real\-time stream of database activity\.

**Topics**
+ [Overview of Database Activity Streams](DBActivityStreams.Overview.md)
+ [Network prerequisites for Aurora MySQL database activity streams](#DBActivityStreams.Prereqs)
+ [Starting a database activity stream](DBActivityStreams.Enabling.md)
+ [Getting the status of a database activity stream](DBActivityStreams.Status.md)
+ [Stopping a database activity stream](DBActivityStreams.Disabling.md)
+ [Monitoring database activity streams](DBActivityStreams.Monitoring.md)
+ [Managing access to database activity streams](DBActivityStreams.ManagingAccess.md)

## Network prerequisites for Aurora MySQL database activity streams<a name="DBActivityStreams.Prereqs"></a>

To use Database Activity Streams with Aurora MySQL, all the DB instances that use activity streams within a VPC must be able to access AWS KMS endpoints\. Before enabling database activity streams for your Aurora MySQL cluster, make sure this requirement is satisfied\.

**Important**  
 If the Aurora MySQL DB cluster can't access the AWS KMS endpoint, the activity stream stops\. In that case, Aurora notifies you about this issue using RDS Events\. 

 If the Aurora cluster is publicly available, this requirement is satisfied automatically\.  

If you receive a notification that your Aurora MySQL cluster can't use database activity streams because it can't access a AWS KMS endpoint, check if your Aurora DB cluster is public or private\. If your Aurora DB cluster is private, you must configure it to enable connections\.

For an Aurora DB cluster to be public, it must be marked as publicly accessible\. If you look at the details for the DB cluster in the AWS Management Console, **Publicly Accessible** is **Yes** if this is the case\. The DB cluster must also be in an Amazon VPC public subnet\. For more information about publicly accessible DB instances, see [Working with a DB instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md)\. For more information about public Amazon VPC subnets, see [Your VPC and Subnets](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html)\.

If your Aurora DB cluster isn't publicly accessible and in a VPC public subnet, it is private\. You might keep your Aurora MySQL cluster private and use it with database activity streams\. If so, configure the cluster so that it can connect to Internet addresses through Network Address Translation \(NAT\)\. For more information about configuring NAT in your VPC, see [NAT Gateways](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html)\.

For more information about configuring VPC endpoints, see [VPC Endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints.html)\.