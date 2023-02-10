# Encrypting Amazon Aurora resources<a name="Overview.Encryption"></a>

Amazon Aurora can encrypt your Amazon Aurora DB clusters\. Data that is encrypted at rest includes the underlying storage for DB clusters, its automated backups, read replicas, and snapshots\.

Amazon Aurora encrypted DB clusters use the industry standard AES\-256 encryption algorithm to encrypt your data on the server that hosts your Amazon Aurora DB clusters\. After your data is encrypted, Amazon Aurora handles authentication of access and decryption of your data transparently with a minimal impact on performance\. You don't need to modify your database client applications to use encryption\.

**Note**  
For encrypted and unencrypted DB clusters, data that is in transit between the source and the read replicas is encrypted, even when replicating across AWS Regions\.

**Topics**
+ [Overview of encrypting Amazon Aurora resources](#Overview.Encryption.Overview)
+ [Encrypting an Amazon Aurora DB cluster](#Overview.Encryption.Enabling)
+ [Determining whether encryption is turned on for a DB cluster](#Overview.Encryption.Determining)
+ [Availability of Amazon Aurora encryption](#Overview.Encryption.Availability)
+ [Limitations of Amazon Aurora encrypted DB clusters](#Overview.Encryption.Limitations)

## Overview of encrypting Amazon Aurora resources<a name="Overview.Encryption.Overview"></a>

Amazon Aurora encrypted DB clusters provide an additional layer of data protection by securing your data from unauthorized access to the underlying storage\. You can use Amazon Aurora encryption to increase data protection of your applications deployed in the cloud, and to fulfill compliance requirements for encryption at rest\.

For an Amazon Aurora encrypted DB cluster, all DB instances, logs, backups, and snapshots are encrypted\. You can also encrypt a read replica of an Amazon Aurora encrypted cluster\. Amazon Aurora uses an AWS KMS key to encrypt these resources\. For more information about KMS keys, see [AWS KMS keys](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#kms_keys) in the *AWS Key Management Service Developer Guide* and [AWS KMS key management](Overview.Encryption.Keys.md)\. Each DB instance in the DB cluster is encrypted using the same KMS key as the DB cluster\. If you copy an encrypted snapshot, you can use a different KMS key to encrypt the target snapshot than the one that was used to encrypt the source snapshot\.

You can use an AWS managed key, or you can create customer managed keys\. To manage the customer managed keys used for encrypting and decrypting your Amazon Aurora resources, you use the [AWS Key Management Service \(AWS KMS\)](https://docs.aws.amazon.com/kms/latest/developerguide/)\. AWS KMS combines secure, highly available hardware and software to provide a key management system scaled for the cloud\. Using AWS KMS, you can create customer managed keys and define the policies that control how these customer managed keys can be used\. AWS KMS supports CloudTrail, so you can audit KMS key usage to verify that customer managed keys are being used appropriately\. You can use your customer managed keys with Amazon Aurora and supported AWS services such as Amazon S3, Amazon EBS, and Amazon Redshift\. For a list of services that are integrated with AWS KMS, see [AWS Service Integration](http://aws.amazon.com/kms/features/#AWS_Service_Integration)\.

## Encrypting an Amazon Aurora DB cluster<a name="Overview.Encryption.Enabling"></a>

To encrypt a new DB cluster, choose **Enable encryption** on the console\. For information on creating a DB cluster, see [Creating an Amazon Aurora DB cluster](Aurora.CreateInstance.md)\.

If you use the [create\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) AWS CLI command to create an encrypted DB cluster, set the `--storage-encrypted` parameter\. If you use the [CreateDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) API operation, set the `StorageEncrypted` parameter to true\.

When you create an encrypted DB cluster, you can choose a customer managed key or the AWS managed key for Amazon Aurora to encrypt your DB cluster\. If you don't specify the key identifier for a customer managed key, Amazon Aurora uses the AWS managed key for your new DB cluster\. Amazon Aurora creates an AWS managed key for Amazon Aurora for your AWS account\. Your AWS account has a different AWS managed key for Amazon Aurora for each AWS Region\.

For more information about KMS keys, see [AWS KMS keys](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#kms_keys) in the *AWS Key Management Service Developer Guide*\.

Once you have created an encrypted DB cluster, you can't change the KMS key used by that DB cluster\. Therefore, be sure to determine your KMS key requirements before you create your encrypted DB cluster\.

If you use the AWS CLI `create-db-cluster` command to create an encrypted DB cluster with a customer managed key, set the `--kms-key-id` parameter to any key identifier for the KMS key\. If you use the Amazon RDS API `CreateDBInstance` operation, set the `KmsKeyId` parameter to any key identifier for the KMS key\. To use a customer managed key in a different AWS account, specify the key ARN or alias ARN\.

**Important**  
Amazon Aurora can lose access to the KMS key for a DB cluster\. For example, Aurora loses access when the KMS key isn't enabled, or when Aurora access to a KMS key is revoked\. In these cases, the encrypted DB cluster goes into `inaccessible-encryption-credentials-recoverable` state\. The DB cluster remains in this state for seven days\. When you start the DB cluster during that time, it checks if the KMS key is active and recovers the DB cluster if it is\. Restart the DB cluster using the AWS CLI command [start\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/start-db-cluster.html)\. Currently, you can't start a DB cluster in this state using the AWS Management Console\.  
If the DB cluster isn't recovered, then it goes into the terminal `inaccessible-encryption-credentials` state\. In this case, you can only restore the DB cluster from a backup\. We strongly recommend that you always turn on backups for encrypted DB instances to guard against the loss of encrypted data in your databases\.

## Determining whether encryption is turned on for a DB cluster<a name="Overview.Encryption.Determining"></a>

You can use the AWS Management Console, AWS CLI, or RDS API to determine whether encryption at rest is turned on for a DB cluster\.

### Console<a name="Overview.Encryption.Determining.CON"></a>

**To determine whether encryption at rest is turned on for a DB cluster**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Databases**\.

1. Choose the name of the DB cluster that you want to check to view its details\.

1. Choose the **Configuration** tab and check the **Encryption** value\.

   It shows either **Enabled** or **Not enabled**\.  
![\[Checking encryption at rest for a DB cluster\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/encryption-check-db-cluster.png)

### AWS CLI<a name="Overview.Encryption.Determining.CLI"></a>

To determine whether encryption at rest is turned on for a DB cluster by using the AWS CLI, call the [describe\-db\-clusters](https://docs.aws.amazon.com/cli/latest/reference/rds/describe-db-clusters.html) command with the following option: 
+ `--db-cluster-identifier` – The name of the DB cluster\.

The following example uses a query to return either `TRUE` or `FALSE` regarding encryption at rest for the `mydb` DB cluster\.

**Example**  

```
1. aws rds describe-db-clusters --db-cluster-identifier mydb --query "*[].{StorageEncrypted:StorageEncrypted}" --output text
```

### RDS API<a name="Overview.Encryption.Determining.API"></a>

To determine whether encryption at rest is turned on for a DB cluster by using the Amazon RDS API, call the [DescribeDBClusters](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBClusters.html) operation with the following parameter: 
+ `DBClusterIdentifier` – The name of the DB cluster\.

## Availability of Amazon Aurora encryption<a name="Overview.Encryption.Availability"></a>

Amazon Aurora encryption is currently available for all database engines and storage types\.

**Note**  
Amazon Aurora encryption is not available for the db\.t2\.micro DB instance class\.

## Limitations of Amazon Aurora encrypted DB clusters<a name="Overview.Encryption.Limitations"></a>

The following limitations exist for Amazon Aurora encrypted DB clusters:
+ You can't turn off encryption on an encrypted DB cluster\.
+ You can't create an encrypted snapshot of an unencrypted DB cluster\.
+ A snapshot of an encrypted DB cluster must be encrypted using the same KMS key as the DB cluster\.
+ You can't convert an unencrypted DB cluster to an encrypted one\. However, you can restore an unencrypted snapshot to an encrypted Aurora DB cluster\. To do this, specify a KMS key when you restore from the unencrypted snapshot\.
+ You can't create an encrypted Aurora Replica from an unencrypted Aurora DB cluster\. You can't create an unencrypted Aurora Replica from an encrypted Aurora DB cluster\.
+ To copy an encrypted snapshot from one AWS Region to another, you must specify the KMS key in the destination AWS Region\. This is because KMS keys are specific to the AWS Region that they are created in\.

  The source snapshot remains encrypted throughout the copy process\. Amazon Aurora uses envelope encryption to protect data during the copy process\. For more information about envelope encryption, see [ Envelope encryption](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#enveloping) in the *AWS Key Management Service Developer Guide*\.
+ You can't unencrypt an encrypted DB cluster\. However, you can export data from an encrypted DB cluster and import the data into an unencrypted DB cluster\.