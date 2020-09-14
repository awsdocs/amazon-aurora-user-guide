# Encrypting Amazon Aurora Resources<a name="Overview.Encryption"></a>

You can encrypt your Amazon Aurora DB clusters and snapshots at rest by enabling the encryption option for your Amazon Aurora DB clusters\. Data that is encrypted at rest includes the underlying storage for DB clusters, its automated backups, read replicas, and snapshots\.

Amazon Aurora encrypted DB clusters use the industry standard AES\-256 encryption algorithm to encrypt your data on the server that hosts your Amazon Aurora DB clusters\. After your data is encrypted, Amazon Aurora handles authentication of access and decryption of your data transparently with a minimal impact on performance\. You don't need to modify your database client applications to use encryption\.

**Note**  
For encrypted and unencrypted DB clusters, data that is in transit between the source and the read replicas is encrypted, even when replicating across AWS Regions\.

**Topics**
+ [Overview of Encrypting Amazon Aurora Resources](#Overview.Encryption.Overview)
+ [Enabling Encryption for an Amazon Aurora DB Cluster](#Overview.Encryption.Enabling)
+ [Availability of Amazon Aurora Encryption](#Overview.Encryption.Availability)
+ [Limitations of Amazon Aurora Encrypted DB Clusters](#Overview.Encryption.Limitations)

## Overview of Encrypting Amazon Aurora Resources<a name="Overview.Encryption.Overview"></a>

Amazon Aurora encrypted DB clusters provide an additional layer of data protection by securing your data from unauthorized access to the underlying storage\. You can use Amazon Aurora encryption to increase data protection of your applications deployed in the cloud, and to fulfill compliance requirements for data\-at\-rest encryption\.

To manage the customer master keys \(CMKs\) used for encrypting and decrypting your Amazon Aurora resources, you use the [AWS Key Management Service \(AWS KMS\)](https://docs.aws.amazon.com/kms/latest/developerguide/)\. AWS KMS combines secure, highly available hardware and software to provide a key management system scaled for the cloud\. Using AWS KMS, you can create CMKs and define the policies that control how these CMKs can be used\. AWS KMS supports CloudTrail, so you can audit CMK usage to verify that CMKs are being used appropriately\. You can use your AWS KMS CMKs with Amazon Aurora and supported AWS services such as Amazon S3, Amazon EBS, and Amazon Redshift\. For a list of services that support AWS KMS, see [Supported Services](https://docs.aws.amazon.com/kms/latest/developerguide/services.html) in the *AWS Key Management Service Developer Guide*\.

For an Amazon Aurora encrypted DB cluster, all logs, backups, and snapshots are encrypted\. You can also encrypt a read replica of an Amazon Aurora encrypted cluster\. The encryption for the read replica is protected by the AWS Region's AWS KMS CMK\.

## Enabling Encryption for an Amazon Aurora DB Cluster<a name="Overview.Encryption.Enabling"></a>

To enable encryption for a new DB cluster, choose **Enable encryption** on the console\. For information on creating a DB cluster, see [Creating an Amazon Aurora DB Cluster](Aurora.CreateInstance.md)\.

If you use the [create\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) AWS CLI command to create an encrypted DB cluster, set the `--storage-encrypted` parameter to true\. If you use the [CreateDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) API operation, set the `StorageEncrypted` parameter to true\.

When you create an encrypted DB cluster, you can also supply the AWS KMS key identifier for your customer master key \(CMK\)\. If you don't specify an AWS KMS key identifier, then Amazon Aurora uses your default CMK for your new DB cluster\. AWS KMS creates your default CMK for Amazon Aurora for your AWS account\. Your AWS account has a different default CMK for each AWS Region\.

Once you have created an encrypted DB cluster, you can't change the type of CMK used by that DB cluster\. Therefore, be sure to determine your CMK requirements before you create your encrypted DB cluster\.

If you use the AWS CLI `create-db-cluster` command to create an encrypted DB cluster, set the `--kms-key-id` parameter to the Amazon Resource Name \(ARN\) for the AWS KMS CMK for the DB cluster\. If you use the RDS API `CreateDBCluster` action, set the `KmsKeyId` parameter to the ARN for your AWS KMS CMK for the DB cluster\.

You can use the ARN of a CMK from another account to encrypt a DB cluster\. Or you might create a DB cluster with the same AWS account that owns the AWS KMS CMK used to encrypt that new DB cluster\. In this case, the key identifier that you pass can be the AWS KMS CMK alias instead of the CMK's ARN\.

**Important**  
In some cases, Amazon Aurora can lose access to the CMK for a DB cluster\. For example, Aurora loses access when RDS access to a CMK is revoked\. In these cases, the encrypted DB cluster goes into a terminal state and you can only restore the DB cluster from a backup\. We strongly recommend that you always enable backups for encrypted DB clusters to guard against the loss of encrypted data in your databases\.

## Availability of Amazon Aurora Encryption<a name="Overview.Encryption.Availability"></a>

Amazon Aurora encryption is currently available for all database engines and storage types\.

**Note**  
Amazon Aurora encryption is not available for the db\.t2\.micro DB instance class\.

## Limitations of Amazon Aurora Encrypted DB Clusters<a name="Overview.Encryption.Limitations"></a>

The following limitations exist for Amazon Aurora encrypted DB clusters:
+ DB clusters that are encrypted can't be modified to disable encryption\.
+ You can't convert an unencrypted DB cluster to an encrypted one\. However, you can restore an unencrypted Aurora DB cluster snapshot to an encrypted Aurora DB cluster\. To do this, specify a CMK when you restore from the unencrypted DB cluster snapshot\.
+ You can't create an encrypted Aurora Replica from an unencrypted Aurora DB cluster\. You can't create an unencrypted Aurora Replica from an encrypted Aurora DB cluster\.
+ To copy an encrypted snapshot from one AWS Region to another, you must specify the AWS KMS key identifier of the destination AWS Region\. This is because AWS KMS CMKs are specific to the AWS Region that they are created in\.

  The source snapshot remains encrypted throughout the copy process\. AWS KMS uses envelope encryption to protect data during the copy process\. For more information about envelope encryption, see [ Envelope Encryption](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#enveloping)\.