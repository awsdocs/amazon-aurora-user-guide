# Enabling Network Communication from Amazon Aurora MySQL to Other AWS Services<a name="AuroraMySQL.Integrating.Authorizing.Network"></a>

To use certain other AWS services with Amazon Aurora, the network configuration of your Aurora DB cluster must allow outbound connections to endpoints for those services\. The following operations require this network configuration\.
+  Invoking AWS Lambda functions\. To learn about this feature, see [Invoking a Lambda Function with an Aurora MySQL Native Function](AuroraMySQL.Integrating.Lambda.md#AuroraMySQL.Integrating.NativeLambda)\. 
+  Accessing files from Amazon S3\. To learn about this feature, see [Loading Data into an Amazon Aurora MySQL DB Cluster from Text Files in an Amazon S3 Bucket](AuroraMySQL.Integrating.LoadFromS3.md) and [Saving Data from an Amazon Aurora MySQL DB Cluster into Text Files in an Amazon S3 Bucket](AuroraMySQL.Integrating.SaveIntoS3.md)\. 
+  Accessing AWS KMS endpoints\. AWS KMS access is required to use database activity streams with Aurora MySQL\. To learn about this feature, see [Using Database Activity Streams with Amazon Aurora](DBActivityStreams.md)\. 

Aurora returns the following error messages if it can't connect to a service endpoint\.

```
ERROR 1871 (HY000): S3 API returned error: Network Connection
```

```
ERROR 1873 (HY000): Lambda API returned error: Network Connection. Unable to connect to endpoint
```

```
ERROR 1815 (HY000): Internal error: Unable to initialize S3Stream
```

 For database activity streams using Aurora MySQL, the activity stream stops functioning if the DB cluster can't access the AWS KMS endpoint\. Aurora notifies you about this issue using RDS Events\. 

If you encounter these messages while using the corresponding AWS services, check if your Aurora DB cluster is public or private\. If your Aurora DB cluster is private, you must configure it to enable connections\.

For an Aurora DB cluster to be public, it must be marked as publicly accessible\. If you look at the details for the DB cluster in the AWS Management Console, **Publicly Accessible** is **Yes** if this is the case\. The DB cluster must also be in an Amazon VPC public subnet\. For more information about publicly accessible DB instances, see [Working with a DB Instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md)\. For more information about public Amazon VPC subnets, see [Your VPC and Subnets](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html)\.

If your Aurora DB cluster isn't publicly accessible and in a VPC public subnet, it is private\. You might have a DB cluster that is private and want to use one of the features that requires this network configuration\. If so, configure the cluster so that it can connect to Internet addresses through Network Address Translation \(NAT\)\. As an alternative for Amazon S3, you can instead configure the VPC to have a VPC endpoint for Amazon S3 associated with the DB cluster's route table\. For more information about configuring NAT in your VPC, see [NAT Gateways](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html)\. For more information about configuring VPC endpoints, see [VPC Endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints.html)\. 