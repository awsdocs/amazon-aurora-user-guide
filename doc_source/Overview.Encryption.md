# Encrypting Amazon Aurora resources<a name="Overview.Encryption"></a>

Amazon Aurora can encrypt your Amazon Aurora DB clusters\. Data that is encrypted at rest includes the underlying storage for DB clusters, its automated backups, read replicas, and snapshots\.

Amazon Aurora encrypted DB clusters use the industry standard AES\-256 encryption algorithm to encrypt your data on the server that hosts your Amazon Aurora DB clusters\. After your data is encrypted, Amazon Aurora handles authentication of access and decryption of your data transparently with a minimal impact on performance\. You don't need to modify your database client applications to use encryption\.

**Note**  
For encrypted and unencrypted DB clusters, data that is in transit between the source and the read replicas is encrypted, even when replicating across AWS Regions\.

**Topics**
+ [Overview of encrypting Amazon Aurora resources](#Overview.Encryption.Overview)
+ [Enabling encryption for an Amazon Aurora DB cluster](#Overview.Encryption.Enabling)
+ [Availability of Amazon Aurora encryption](#Overview.Encryption.Availability)
+ [Limitations of Amazon Aurora encrypted DB clusters](#Overview.Encryption.Limitations)

## Overview of encrypting Amazon Aurora resources<a name="Overview.Encryption.Overview"></a>

Amazon Aurora encrypted DB clusters provide an additional layer of data protection by securing your data from unauthorized access to the underlying storage\. You can use Amazon Aurora encryption to increase data protection of your applications deployed in the cloud, and to fulfill compliance requirements for encryption at rest\.

For an Amazon Aurora encrypted DB cluster, all DB instances, logs, backups, and snapshots are encrypted\. You can also encrypt a read replica of an Amazon Aurora encrypted cluster\. Amazon Aurora uses an AWS KMS customer master key \(CMK\) to encrypt these resources\. For more information about CMKs, see [Customer master keys \(CMKs\)](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#master_keys) in the *AWS Key Management Service Developer Guide*\. Each DB instance in the DB cluster is encrypted using the same CMK as the DB cluster\. If you copy an encrypted snapshot, you can use a different CMK to encrypt the target snapshot than the one that was used to encrypt the source snapshot\.

You can use an AWS managed CMK, or you can create customer managed CMKs\. To manage the CMKs used for encrypting and decrypting your Amazon Aurora resources, you use the [AWS Key Management Service \(AWS KMS\)](https://docs.aws.amazon.com/kms/latest/developerguide/)\. AWS KMS combines secure, highly available hardware and software to provide a key management system scaled for the cloud\. Using AWS KMS, you can create CMKs and define the policies that control how these CMKs can be used\. AWS KMS supports CloudTrail, so you can audit CMK usage to verify that CMKs are being used appropriately\. You can use your AWS KMS CMKs with Amazon Aurora and supported AWS services such as Amazon S3, Amazon EBS, and Amazon Redshift\. For a list of services that are integrated with AWS KMS, see [AWS Service Integration](http://aws.amazon.com/kms/features/#AWS_Service_Integration)\.

## Enabling encryption for an Amazon Aurora DB cluster<a name="Overview.Encryption.Enabling"></a>

To enable encryption for a new DB cluster, choose **Enable encryption** on the console\. For information on creating a DB cluster, see [Creating an Amazon Aurora DB cluster](Aurora.CreateInstance.md)\.

If you use the [create\-db\-cluster](https://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) AWS CLI command to create an encrypted DB cluster, set the `--storage-encrypted` parameter\. If you use the [CreateDBCluster](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) API operation, set the `StorageEncrypted` parameter to true\.

When you create an encrypted DB cluster, you can choose a customer managed CMK or the AWS managed CMK for Amazon Aurora to encrypt your DB cluster\. If you don't specify the key identifier for a customer managed CMK, Amazon Aurora uses the AWS managed CMK for your new DB cluster\. Amazon Aurora creates an AWS managed CMK for Amazon Aurora for your AWS account\. Your AWS account has a different AWS managed CMK for Amazon Aurora for each AWS Region\.

Once you have created an encrypted DB cluster, you can't change the CMK used by that DB cluster\. Therefore, be sure to determine your CMK requirements before you create your encrypted DB cluster\.

If you use the AWS CLI `create-db-cluster` command to create an encrypted DB cluster with a customer managed CMK, set the `--kms-key-id` parameter to any key identifier for the CMK\. If you use the Amazon RDS API `CreateDBInstance` operation, set the `KmsKeyId` parameter to any key identifier for the CMK\. To use a customer managed CMK in a different AWS account, specify the key ARN or alias ARN\.

**Important**  
In some cases, Amazon Aurora can lose access to the CMK for a DB cluster\. For example, Aurora loses access when RDS access to a CMK is revoked\. In these cases, the encrypted DB cluster goes into a terminal state, and you can only restore the DB cluster from a backup\. We strongly recommend that you always enable backups for encrypted DB clusters to guard against the loss of encrypted data in your databases\.

## Availability of Amazon Aurora encryption<a name="Overview.Encryption.Availability"></a>

Amazon Aurora encryption is currently available for all database engines and storage types\.

**Note**  
Amazon Aurora encryption is not available for the db\.t2\.micro DB instance class\.

## Limitations of Amazon Aurora encrypted DB clusters<a name="Overview.Encryption.Limitations"></a>

The following limitations exist for Amazon Aurora encrypted DB clusters:
+ You can't disable encryption on an encrypted DB cluster\.
+ You can't create an encrypted snapshot of an unencrypted DB cluster\.
+ A snapshot of an encrypted cluster must be encrypted using the same CMK as the DB cluster\.
+ You can't convert an unencrypted DB cluster to an encrypted one\. However, you can restore an unencrypted snapshot to an encrypted Aurora DB cluster\. To do this, specify a CMK when you restore from the unencrypted snapshot\.
+ You can't create an encrypted Aurora Replica from an unencrypted Aurora DB cluster\. You can't create an unencrypted Aurora Replica from an encrypted Aurora DB cluster\.
+ To copy an encrypted snapshot from one AWS Region to another, you must specify the CMK in the destination AWS Region\. This is because CMKs are specific to the AWS Region that they are created in\.

  The source snapshot remains encrypted throughout the copy process\. Amazon Aurora uses envelope encryption to protect data during the copy process\. For more information about envelope encryption, see [ Envelope encryption](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#enveloping) in the *AWS Key Management Service Developer Guide*\.