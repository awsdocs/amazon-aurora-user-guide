# Network prerequisites for Aurora MySQL database activity streams<a name="DBActivityStreams.Prereqs"></a>

In the following section, you can find how to configure your virtual private cloud \(VPC\) for use with database activity streams\.

**Topics**
+ [Prerequisites for AWS KMS endpoints](#DBActivityStreams.Prereqs.KMS)
+ [Prerequisites for public availability](#DBActivityStreams.Prereqs.Public)
+ [Prerequisites for private availability](#DBActivityStreams.Prereqs.Private)

## Prerequisites for AWS KMS endpoints<a name="DBActivityStreams.Prereqs.KMS"></a>

Instances in an Aurora MySQL cluster that use activity streams must be able to access AWS KMS endpoints\. Make sure this requirement is satisfied before enabling database activity streams for your Aurora MySQL cluster\. If the Aurora cluster is publicly available, this requirement is satisfied automatically\.

**Important**  
If the Aurora MySQL DB cluster can't access the AWS KMS endpoint, the activity stream stops\. In that case, Aurora notifies you about this issue using RDS Events\. 

## Prerequisites for public availability<a name="DBActivityStreams.Prereqs.Public"></a>

For an Aurora DB cluster to be public, it must meet the following requirements:
+ **Publicly Accessible** is **Yes** in the AWS Management Console cluster details page\.
+ The DB cluster is in an Amazon VPC public subnet\. For more information about publicly accessible DB instances, see [Working with a DB instance in a VPC](USER_VPC.WorkingWithRDSInstanceinaVPC.md)\. For more information about public Amazon VPC subnets, see [Your VPC and Subnets](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html)\.

## Prerequisites for private availability<a name="DBActivityStreams.Prereqs.Private"></a>

If your Aurora DB cluster isn't publicly accessible, and it's in a VPC public subnet, it's private\. To keep your cluster private and use it with database activity streams, you have the following options:
+ Configure Network Address Translation \(NAT\) in your VPC\. For more information, see [NAT Gateways](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html)\.
+ Create an AWS KMS endpoint in your VPC\. This option is recommended because it's easier to configure\.

**To create an AWS KMS endpoint in your VPC**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Endpoints**\.

1. Choose **Create Endpoint**\.

   The **Create Endpoint** page appears\.

1. Do the following:
   + In **Service category**, choose **AWS services**\.
   + In **Service Name**, choose **com\.amazonaws\.*region*\.kms**, where *region* is the AWS Region where your cluster is located\.
   + For **VPC**, choose the VPC where your cluster is located\.

1. Choose **Create Endpoint**\.

For more information about configuring VPC endpoints, see [VPC Endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints.html)\.