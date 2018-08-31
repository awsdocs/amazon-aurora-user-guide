# Encrypting Amazon RDS Resources<a name="Overview.Encryption"></a>

You can encrypt your Amazon RDS DB clusters and snapshots at rest by enabling the encryption option for your Amazon RDS DB clusters\. Data that is encrypted at rest includes the underlying storage for a DB clusters, its automated backups, Read Replicas, and snapshots\.

Amazon RDS encrypted DB clusters use the industry standard AES\-256 encryption algorithm to encrypt your data on the server that hosts your Amazon RDS DB clusters\. Once your data is encrypted, Amazon RDS handles authentication of access and decryption of your data transparently with a minimal impact on performance\. You don't need to modify your database client applications to use encryption\. 

**Topics**
+ [Overview of Encrypting Amazon RDS Resources](#Overview.Encryption.Overview)
+ [Enabling Amazon RDS Encryption for a DB Cluster](#Overview.Encryption.Enabling)
+ [Availability of Amazon RDS Encryption](#Overview.Encryption.Availability)
+ [Managing Amazon RDS Encryption Keys](#Overview.Encryption.Keys)
+ [Limitations of Amazon RDS Encrypted DB Clusters](#Overview.Encryption.Limitations)

## Overview of Encrypting Amazon RDS Resources<a name="Overview.Encryption.Overview"></a>

Amazon RDS encrypted DB clusters provide an additional layer of data protection by securing your data from unauthorized access to the underlying storage\. You can use Amazon RDS encryption to increase data protection of your applications deployed in the cloud, and to fulfill compliance requirements for data\-at\-rest encryption\.

To manage the keys used for encrypting and decrypting your Amazon RDS resources, you use the [AWS Key Management Service \(AWS KMS\)](http://docs.aws.amazon.com/kms/latest/developerguide/)\. AWS KMS combines secure, highly available hardware and software to provide a key management system scaled for the cloud\. Using AWS KMS, you can create encryption keys and define the policies that control how these keys can be used\. AWS KMS supports CloudTrail, so you can audit key usage to verify that keys are being used appropriately\. Your AWS KMS keys can be used in combination with Amazon RDS and supported AWS services such as Amazon Simple Storage Service \(Amazon S3\), Amazon Elastic Block Store \(Amazon EBS\), and Amazon Redshift\. For a list of services that support AWS KMS, go to [Supported Services](http://docs.aws.amazon.com/kms/latest/developerguide/services.html) in the *AWS Key Management Service Developer Guide*\.

For an Amazon RDS encrypted DB cluster, all logs, backups, and snapshots are encrypted\. You can also encrypt a Read Replica of an Amazon RDS encrypted cluster\. You encrypt using the encryption key for the Read Replica's region\.

For encrypted and unencrypted Amazon RDS DB clusters with cross\-region Read Replicas, data sent between the source and the Read Replicas is encrypted\.

## Enabling Amazon RDS Encryption for a DB Cluster<a name="Overview.Encryption.Enabling"></a>

To enable encryption for a new DB cluster, choose **Enable encryption** on the Amazon RDS console\. For information on creating a DB cluster, see [Creating an Amazon Aurora DB Cluster](Aurora.CreateInstance.md)\.

If you use the [create\-db\-cluster](http://docs.aws.amazon.com/cli/latest/reference/rds/create-db-cluster.html) AWS CLI command to create an encrypted RDS DB cluster, set the `--storage-encrypted` parameter to true\. If you use the [CreateDBCluster](http://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CreateDBCluster.html) API action, set the `StorageEncrypted` parameter to true\.

When you create an encrypted DB cluster, you can also supply the AWS KMS key identifier for your encryption key\. If you don't specify an AWS KMS key identifier, then Amazon RDS uses your default encryption key for your new DB cluster\. AWS KMS creates your default encryption key for Amazon RDS for your AWS account\. Your AWS account has a different default encryption key for each AWS Region\.

Once you have created an encrypted DB cluster, you cannot change the encryption key for that cluster\. Therefore, be sure to determine your encryption key requirements before you create your encrypted DB cluster\.

If you use the AWS CLI `create-db-cluster` command to create an encrypted RDS DB cluster, set the `--kms-key-id` parameter to the Amazon Resource Name \(ARN\) for the AWS KMS encryption key for the DB cluster\. If you use the Amazon RDS API `CreateDBCluster` action, set the `KmsKeyId` parameter to the ARN for your AWS KMS key for the DB cluster\.

You can use the ARN of a key from another account to encrypt an DB cluster\. Or you might create a DB cluster with the same AWS account that owns the AWS KMS encryption key used to encrypt that new DB cluster\. In this case, the AWS KMS key ID that you pass can be the AWS KMS key alias instead of the key's ARN\.

**Important**  
If Amazon RDS loses access to the encryption key for a DB cluster—for example, when RDS access to a key is revoked—then the encrypted DB cluster goes into a terminal state\. In this case, you can only restore the DB cluster from a backup\. We strongly recommend that you always enable backups for encrypted DB clusters to guard against the loss of encrypted data in your databases\.

## Availability of Amazon RDS Encryption<a name="Overview.Encryption.Availability"></a>

Amazon RDS encryption is currently available for all database engines and storage types\. Amazon RDS encryption is not currently available in the China \(Beijing\) region\.

Amazon RDS encryption is available for the following DB instance classes:


| Instance Type | Instance Class | 
| --- | --- | 
| Memory Optimized \(R3\) |  db\.r3\.large db\.r3\.xlarge db\.r3\.2xlarge db\.r3\.4xlarge db\.r3\.8xlarge  | 
| Memory Optimized \(R4\) |  db\.r4\.large db\.r4\.xlarge db\.r4\.2xlarge db\.r4\.4xlarge db\.r4\.8xlarge db\.r4\.16xlarge  | 
| Burst Capable \(T2\) |  db\.t2\.small db\.t2\.medium db\.t2\.large db\.t2\.xlarge db\.t2\.2xlarge  | 

## Managing Amazon RDS Encryption Keys<a name="Overview.Encryption.Keys"></a>

You can manage keys used for Amazon RDS encrypted DB clusters using the [AWS Key Management Service \(AWS KMS\)](http://docs.aws.amazon.com/kms/latest/developerguide/) in the IAM console\. If you want full control over a key, then you must create a customer\-managed key\.

You can't delete, revoke, or rotate default keys provisioned by AWS KMS\. You can't share a snapshot that has been encrypted using the default AWS KMS encryption key of the AWS account that shared the snapshot\.

You can view audit logs of every action taken with a customer\-managed key by using [AWS CloudTrail](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\.

**Important**  
If you disable the key for an encrypted DB clusters, you cannot read from or write to that DB cluster\. When Amazon RDS encounters a DB cluster encrypted by a key that Amazon RDS doesn't have access to, Amazon RDS puts the DB cluster into a terminal state\. In this state, the DB cluster is no longer available and the current state of the database can't be recovered\. To restore the DB cluster, you must re\-enable access to the encryption key for Amazon RDS, and then restore the DB cluster from a backup\.

## Limitations of Amazon RDS Encrypted DB Clusters<a name="Overview.Encryption.Limitations"></a>

The following limitations exist for Amazon RDS encrypted DB clusters:
+ DB clusters that are encrypted can't be modified to disable encryption\.
+ You can't convert a unencrypted DB cluster to an encrypted one\. However, you can restore an unencrypted Aurora DB cluster snapshot to an encrypted Aurora DB cluster if you specify a KMS encryption key when you restore from the unencrypted DB cluster snapshot\.
+ You can't create an encrypted Aurora Replica for an unencrypted Aurora DB cluster\. You can't create an unencrypted Aurora Replica for an encrypted Aurora DB cluster\.
+ To copy an encrypted snapshot from one region to another, you must specify the KMS key identifier of the destination region\. This is because KMS encryption keys are specific to the region that they are created in\.

   The source snapshot remains encrypted throughout the copy process\. AWS Key Management Service uses envelope encryption to protect data during the copy process\. For more information about envelope encryption, see [ Envelope Encryption](http://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#enveloping)\.