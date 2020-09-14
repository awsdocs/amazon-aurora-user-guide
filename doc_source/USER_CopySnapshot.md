# Copying a DB Cluster Snapshot<a name="USER_CopySnapshot"></a>

With Amazon RDS, you can copy automated or manual DB cluster snapshots\. After you copy a snapshot, the copy is a manual snapshot\. 

You can copy a snapshot within the same AWS Region, you can copy a snapshot across AWS Regions, and you can copy shared snapshots\. 

You can't copy a DB cluster snapshot across Regions and accounts in a single step\. Perform one step for each of these copy actions\. As an alternative to copying, you can also share manual snapshots with other AWS accounts\. For more information, see [Sharing a DB Cluster Snapshot](USER_ShareSnapshot.md)\. 

**Note**  
 Amazon bills you based upon the amount of Aurora backup and snapshot data you keep and the period of time that you keep it\. For information about the storage associated with Aurora backups and snapshots, see [Understanding Aurora Backup Storage Usage](aurora-storage-backup.md)\. For pricing information about Aurora storage, see [Amazon RDS for Aurora Pricing](https://aws.amazon.com/rds/aurora/pricing)\. 

## Limitations<a name="USER_CopySnapshot.Limitations"></a>

The following are some limitations when you copy snapshots: 
+ You can't copy a snapshot to or from the following AWS Regions: China \(Beijing\) or China \(Ningxia\)\. 
+ You can copy a snapshot between AWS GovCloud \(US\-East\) and AWS GovCloud \(US\-West\), but you can't copy a snapshot between these AWS GovCloud \(US\) Regions and other AWS Regions\.
+ If you delete a source snapshot before the target snapshot becomes available, the snapshot copy may fail\. Verify that the target snapshot has a status of `AVAILABLE` before you delete a source snapshot\. 
+ You can have up to five snapshot copy requests in progress to a single destination Region per account\.
+ Depending on the Regions involved and the amount of data to be copied, a cross\-Region snapshot copy can take hours to complete\. If there is a large number of cross\-Region snapshot copy requests from a given source AWS Region, Amazon RDS might put new cross\-Region copy requests from that source AWS Region into a queue until some in\-progress copies complete\. No progress information is displayed about copy requests while they are in the queue\. Progress information is displayed when the copy starts\. 

## Snapshot Retention<a name="USER_CopySnapshot.Retention"></a>

Amazon RDS deletes automated snapshots at the end of their retention period, when you disable automated snapshots for a DB cluster, or when you delete a DB cluster\. If you want to keep an automated snapshot for a longer period, copy it to create a manual snapshot, which is retained until you delete it\. Amazon RDS storage costs might apply to manual snapshots if they exceed your default storage space\. 

For more information about backup storage costs, see [Amazon RDS Pricing](https://aws.amazon.com/rds/pricing/)\. 

## Copying Shared Snapshots<a name="USER_CopySnapshot.Shared"></a>

You can copy snapshots shared to you by other AWS accounts\. If you are copying an encrypted snapshot that has been shared from another AWS account, you must have access to the AWS KMS customer master key \(CMK\) that was used to encrypt the snapshot\. 

You can only copy a shared DB cluster snapshot, whether encrypted or not, in the same AWS Region\. For more information, see [Sharing an Encrypted Snapshot](USER_ShareSnapshot.md#USER_ShareSnapshot.Encrypted)\. 

## Handling Encryption<a name="USER_CopySnapshot.Encryption"></a>

You can copy a snapshot that has been encrypted using an AWS KMS customer master key \(CMK\)\. If you copy an encrypted snapshot, the copy of the snapshot must also be encrypted\. If you copy an encrypted snapshot within the same AWS Region, you can encrypt the copy with the same AWS KMS CMK as the original snapshot, or you can specify a different AWS KMS CMK\. If you copy an encrypted snapshot across Regions, you can't use the same AWS KMS CMK for the copy as used for the source snapshot, because AWS KMS CMKs are Region\-specific\. Instead, you must specify a AWS KMS CMK valid in the destination AWS Region\.

The source snapshot remains encrypted throughout the copy process\. For more information, see [Limitations of Amazon Aurora Encrypted DB Clusters](Overview.Encryption.md#Overview.Encryption.Limitations)\.

**Note**  
For Amazon Aurora DB cluster snapshots, you can't encrypt an unencrypted DB cluster snapshot when you copy the snapshot\.

## Copying Snapshots Across AWS Regions<a name="USER_CopySnapshot.AcrossRegions"></a>

When you copy a snapshot to an AWS Region that is different from the source snapshot's AWS Region, the copy is a full snapshot copy\. A full snapshot copy contains all of the data and metadata required to restore the DB instance\.

Depending on the AWS Regions involved and the amount of data to be copied, a cross\-Region snapshot copy can take hours to complete\. In some cases, there might be a large number of cross\-Region snapshot copy requests from a given source AWS Region\. In these cases, Amazon RDS might put new cross\-Region copy requests from that source AWS Region into a queue until some in\-progress copies complete\. No progress information is displayed about copy requests while they are in the queue\. Progress information is displayed when the copy starts\. 

Cross\-Region snapshot copy isn't supported in the following opt\-in AWS Regions:
+ Africa \(Cape Town\)
+ Asia Pacific \(Hong Kong\)
+ Europe \(Milan\)
+ Middle East \(Bahrain\)

**Note**  
Aurora doesn't support incremental snapshot copying\. Aurora DB cluster snapshot copies are always full copies\.

## Parameter Group Considerations<a name="USER_CopySnapshot.Parameters"></a>

When you copy a snapshot across Regions, the copy doesn't include the parameter group used by the original DB cluster\. When you restore a snapshot to create a new DB cluster, that DB cluster gets the default parameter group for the AWS Region it is created in\. To give the new DB cluster the same parameters as the original, you must do the following: 

1. In the destination AWS Region, create a DB cluster parameter group with the same settings as the original DB cluster\. If one already exists in the new AWS Region, you can use that one\. 

1. After you restore the snapshot in the destination AWS Region, modify the new DB cluster and add the new or existing parameter group from the previous step\. 

## Copying a DB Cluster Snapshot<a name="USER_CopyDBClusterSnapshot.CrossRegion"></a>

Use the procedures in this topic to copy a DB cluster snapshot\. If your source database engine is Aurora, then your snapshot is a DB cluster snapshot\. 

For each AWS account, you can copy up to five DB cluster snapshots at a time from one AWS Region to another\. Copying both encrypted and unencrypted DB cluster snapshots is supported\. If you copy a DB cluster snapshot to another AWS Region, you create a manual DB cluster snapshot that is retained in that AWS Region\. Copying a DB cluster snapshot out of the source AWS Region incurs Amazon RDS data transfer charges\. 

For more information about data transfer pricing, see [Amazon RDS Pricing](https://aws.amazon.com/rds/pricing/)\. 

After the DB cluster snapshot copy has been created in the new AWS Region, the DB cluster snapshot copy behaves the same as all other DB cluster snapshots in that AWS Region\. 

### Console<a name="USER_CopyDBClusterSnapshot.CON"></a>

This procedure works for copying encrypted or unencrypted DB cluster snapshots, in the same AWS Region or across Regions\.

To cancel a copy operation once it is in progress, delete the target DB cluster snapshot while that DB cluster snapshot is in **copying** status\.

**To copy a DB cluster snapshot**

1. Sign in to the AWS Management Console and open the Amazon RDS console at [https://console\.aws\.amazon\.com/rds/](https://console.aws.amazon.com/rds/)\.

1. In the navigation pane, choose **Snapshots**\.

1. Select the check box for the DB cluster snapshot you want to copy\.

1. For **Actions**, choose **Copy Snapshot**\. The **Make Copy of DB Snapshot** page appears\.   
![\[Copy a DB cluster snapshot\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/images/DBClusterSnapshotCopy.png)

1. \(Optional\) To copy the DB cluster snapshot to a different AWS Region, choose that AWS Region for **Destination Region**\.

1. Type the name of the DB cluster snapshot copy in **New DB Snapshot Identifier**\. 

1. To copy tags and values from the snapshot to the copy of the snapshot, choose **Copy Tags**\.

1. Choose **Copy Snapshot**\. 

### Copying an Unencrypted DB Cluster Snapshot by Using the AWS CLI or Amazon RDS API<a name="USER_CopyDBClusterSnapshot.Unencrypted.CrossRegion"></a>

Use the procedures in the following sections to copy an unencrypted DB cluster snapshot by using the AWS CLI or Amazon RDS API\.

To cancel a copy operation once it is in progress, delete the target DB cluster snapshot identified by `--target-db-cluster-snapshot-identifier` or `TargetDBClusterSnapshotIdentifier` while that DB cluster snapshot is in **copying** status\.

#### AWS CLI<a name="USER_CopyDBClusterSnapshot.Unencrypted.CrossRegion.CLI"></a>

To copy a DB cluster snapshot, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-cluster-snapshot.html](https://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-cluster-snapshot.html) command\. If you are copying the snapshot to another AWS Region, run the command in the AWS Region to which the snapshot will be copied\. 

The following options are used to copy an unencrypted DB cluster snapshot:
+ `--source-db-cluster-snapshot-identifier` – The identifier for the DB cluster snapshot to be copied\. If you are copying the snapshot to another AWS Region, this identifier must be in the ARN format for the source AWS Region\.
+ `--target-db-cluster-snapshot-identifier` – The identifier for the new copy of the DB cluster snapshot\.

The following code creates a copy of DB cluster snapshot `arn:aws:rds:us-east-1:123456789012:cluster-snapshot:aurora-cluster1-snapshot-20130805` named `myclustersnapshotcopy` in the AWS Region in which the command is run\. When the copy is made, all tags on the original snapshot are copied to the snapshot copy\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds copy-db-cluster-snapshot \
  --source-db-cluster-snapshot-identifier arn:aws:rds:us-east-1:123456789012:cluster-snapshot:aurora-cluster1-snapshot-20130805 \
  --target-db-cluster-snapshot-identifier myclustersnapshotcopy \
  --copy-tags
```
For Windows:  

```
aws rds copy-db-cluster-snapshot ^
  --source-db-cluster-snapshot-identifier arn:aws:rds:us-east-1:123456789012:cluster-snapshot:aurora-cluster1-snapshot-20130805 ^
  --target-db-cluster-snapshot-identifier myclustersnapshotcopy ^
  --copy-tags
```

#### RDS API<a name="USER_CopyDBClusterSnapshot.Unencrypted.CrossRegion.API"></a>

To copy a DB cluster snapshot, use the Amazon RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CopyDBClusterSnapshot.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CopyDBClusterSnapshot.html) operation\. If you are copying the snapshot to another AWS Region, perform the action in the AWS Region to which the snapshot will be copied\. 

The following parameters are used to copy an unencrypted DB cluster snapshot:
+ `SourceDBClusterSnapshotIdentifier` – The identifier for the DB cluster snapshot to be copied\. If you are copying the snapshot to another AWS Region, this identifier must be in the ARN format for the source AWS Region\.
+ `TargetDBClusterSnapshotIdentifier` – The identifier for the new copy of the DB cluster snapshot\.

The following code creates a copy of a snapshot `arn:aws:rds:us-east-1:123456789012:cluster-snapshot:aurora-cluster1-snapshot-20130805` named `myclustersnapshotcopy` in the us\-west\-1 Region\. When the copy is made, all tags on the original snapshot are copied to the snapshot copy\.

**Example**  

```
https://rds.us-west-1.amazonaws.com/
   ?Action=CopyDBClusterSnapshot
   &CopyTags=true
   &SignatureMethod=HmacSHA256
   &SignatureVersion=4
   &SourceDBSnapshotIdentifier=arn%3Aaws%3Ards%3Aus-east-1%3A123456789012%3Acluster-snapshot%3Aaurora-cluster1-snapshot-20130805
   &TargetDBSnapshotIdentifier=myclustersnapshotcopy
   &Version=2013-09-09
   &X-Amz-Algorithm=AWS4-HMAC-SHA256
   &X-Amz-Credential=AKIADQKE4SARGYLE/20140429/us-west-1/rds/aws4_request
   &X-Amz-Date=20140429T175351Z
   &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
   &X-Amz-Signature=9164337efa99caf850e874a1cb7ef62f3cea29d0b448b9e0e7c53b288ddffed2
```

### Copying an Encrypted DB Cluster Snapshot by Using the AWS CLI or Amazon RDS API<a name="USER_CopyDBClusterSnapshot.Encrypted.CrossRegion"></a>

Use the procedures in the following sections to copy an encrypted DB cluster snapshot by using the AWS CLI or Amazon RDS API\.

To cancel a copy operation once it is in progress, delete the target DB cluster snapshot identified by `--target-db-cluster-snapshot-identifier` or `TargetDBClusterSnapshotIdentifier` while that DB cluster snapshot is in **copying** status\.

#### AWS CLI<a name="USER_CopyDBClusterSnapshot.Encrypted.CrossRegion.CLI"></a>

To copy a DB cluster snapshot, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-cluster-snapshot.html](https://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-cluster-snapshot.html) command\. If you are copying the snapshot to another AWS Region, run the command in the AWS Region to which the snapshot will be copied\. 

The following options are used to copy an encrypted DB cluster snapshot:
+ `--source-region` – If you are copying the snapshot to another AWS Region, specify the AWS Region that the encrypted DB cluster snapshot will be copied from\. 

  If you are copying the snapshot to another AWS Region and you don't specify `source-region`, you must specify the `pre-signed-url` option instead\. The `pre-signed-url` value must be a URL that contains a Signature Version 4 signed request for the `CopyDBClusterSnapshot` action to be called in the source AWS Region where the DB cluster snapshot is copied from\. To learn more about the `pre-signed-url`, see [https://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-cluster-snapshot.html](https://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-cluster-snapshot.html)\. 
+ `--source-db-cluster-snapshot-identifier` – The identifier for the encrypted DB cluster snapshot to be copied\. If you are copying the snapshot to another AWS Region, this identifier must be in the ARN format for the source AWS Region\. If that is the case, the AWS Region specified in `source-db-cluster-snapshot-identifier` must match the AWS Region specified for `--source-region`\. 
+ `--target-db-cluster-snapshot-identifier` – The identifier for the new copy of the encrypted DB cluster snapshot\.
+ `--kms-key-id` – The AWS KMS key identifier for the key to use to encrypt the copy of the DB cluster snapshot\.

  You can optionally use this option if the DB cluster snapshot is encrypted, you are copying the snapshot in the same AWS Region, and you want to specify a new AWS KMS CMK to use to encrypt the copy\. Otherwise, the copy of the DB cluster snapshot is encrypted with the same AWS KMS CMK as the source DB cluster snapshot\. 

  You must use this option if the DB cluster snapshot is encrypted and you are copying the snapshot to another AWS Region\. In that case, you must specify a AWS KMS CMK for the destination AWS Region\.

The following code example copies the encrypted DB cluster snapshot from the us\-west\-2 Region to the us\-east\-1 Region\. The command is called in the us\-east\-1 Region\.

**Example**  
For Linux, macOS, or Unix:  

```
aws rds copy-db-cluster-snapshot \
  --source-db-cluster-snapshot-identifier arn:aws:rds:us-west-2:123456789012:cluster-snapshot:aurora-cluster1-snapshot-20161115 \
  --target-db-cluster-snapshot-identifier myclustersnapshotcopy \
  --source-region us-west-2 \	
  --kms-key-id my-us-east-1-key
```
For Windows:  

```
aws rds copy-db-cluster-snapshot ^
  --source-db-cluster-snapshot-identifier arn:aws:rds:us-west-2:123456789012:cluster-snapshot:aurora-cluster1-snapshot-20161115 ^
  --target-db-cluster-snapshot-identifier myclustersnapshotcopy ^
  --source-region us-west-2 ^	
  --kms-key-id my-us-east-1-key
```

#### RDS API<a name="USER_CopyDBClusterSnapshot.Encrypted.CrossRegion.API"></a>

To copy a DB cluster snapshot, use the Amazon RDS API [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CopyDBClusterSnapshot.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CopyDBClusterSnapshot.html) operation\. If you are copying the snapshot to another AWS Region, perform the action in the AWS Region to which the snapshot will be copied\.

The following parameters are used to copy an encrypted DB cluster snapshot:
+ `SourceDBClusterSnapshotIdentifier` – The identifier for the encrypted DB cluster snapshot to be copied\. If you are copying the snapshot to another AWS Region, this identifier must be in the ARN format for the source AWS Region\. 
+ `TargetDBClusterSnapshotIdentifier` – The identifier for the new copy of the encrypted DB cluster snapshot\.
+ `KmsKeyId` – The AWS KMS key identifier for the key to use to encrypt the copy of the DB cluster snapshot\.

  You can optionally use this parameter if the DB cluster snapshot is encrypted, you are copying the snapshot in the same AWS Region, and you want to specify a new AWS KMS CMK to use to encrypt the copy\. Otherwise, the copy of the DB cluster snapshot is encrypted with the same AWS KMS CMK as the source DB cluster snapshot\. 

  You must use this parameter if the DB cluster snapshot is encrypted and you are copying the snapshot to another AWS Region\. In that case, you must specify a AWS KMS CMK for the destination AWS Region\.
+ `PreSignedUrl` – If you are copying the snapshot to another AWS Region, you must specify the `PreSignedUrl` parameter\. The `PreSignedUrl` value must be a URL that contains a Signature Version 4 signed request for the `CopyDBClusterSnapshot` action to be called in the source AWS Region where the DB cluster snapshot is copied from\. To learn more about using a presigned URL, see [https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CopyDBClusterSnapshot.html](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_CopyDBClusterSnapshot.html)\. 

  To automatically rather than manually generate a presigned URL, use the AWS CLI [https://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-cluster-snapshot.html](https://docs.aws.amazon.com/cli/latest/reference/rds/copy-db-cluster-snapshot.html) command with the `--source-region` option instead\.

The following code example copies the encrypted DB cluster snapshot from the us\-west\-2 Region to the us\-east\-1 Region\. The action is called in the us\-east\-1 Region\. 

**Example**  

```
https://rds.us-east-1.amazonaws.com/
    ?Action=CopyDBClusterSnapshot
    &KmsKeyId=my-us-east-1-key
    &PreSignedUrl=https%253A%252F%252Frds.us-west-2.amazonaws.com%252F
         %253FAction%253DCopyDBClusterSnapshot
         %2526DestinationRegion%253Dus-east-1
         %2526KmsKeyId%253Dmy-us-east-1-key
         %2526SourceDBClusterSnapshotIdentifier%253Darn%25253Aaws%25253Ards%25253Aus-west-2%25253A123456789012%25253Acluster-snapshot%25253Aaurora-cluster1-snapshot-20161115
         %2526SignatureMethod%253DHmacSHA256
         %2526SignatureVersion%253D4
         %2526Version%253D2014-10-31
         %2526X-Amz-Algorithm%253DAWS4-HMAC-SHA256
         %2526X-Amz-Credential%253DAKIADQKE4SARGYLE%252F20161117%252Fus-west-2%252Frds%252Faws4_request
         %2526X-Amz-Date%253D20161117T215409Z
         %2526X-Amz-Expires%253D3600
         %2526X-Amz-SignedHeaders%253Dcontent-type%253Bhost%253Buser-agent%253Bx-amz-content-sha256%253Bx-amz-date
         %2526X-Amz-Signature%253D255a0f17b4e717d3b67fad163c3ec26573b882c03a65523522cf890a67fca613
    &SignatureMethod=HmacSHA256
    &SignatureVersion=4
    &SourceDBClusterSnapshotIdentifier=arn%3Aaws%3Ards%3Aus-west-2%3A123456789012%3Acluster-snapshot%3Aaurora-cluster1-snapshot-20161115
    &TargetDBClusterSnapshotIdentifier=myclustersnapshotcopy
    &Version=2014-10-31
    &X-Amz-Algorithm=AWS4-HMAC-SHA256
    &X-Amz-Credential=AKIADQKE4SARGYLE/20161117/us-east-1/rds/aws4_request
    &X-Amz-Date=20161117T221704Z
    &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
    &X-Amz-Signature=da4f2da66739d2e722c85fcfd225dc27bba7e2b8dbea8d8612434378e52adccf
```

### Copying a DB Cluster Snapshot Across Accounts<a name="USER_CopyDBClusterSnapshot.CrossAccount"></a>

You can enable other AWS accounts to copy DB cluster snapshots that you specify by using the Amazon RDS API `ModifyDBClusterSnapshotAttribute` and `CopyDBClusterSnapshot` actions\. You can only copy DB cluster snapshots across accounts in the same AWS Region\. The cross\-account copying process works as follows, where Account A is making the snapshot available to copy, and Account B is copying it\.

1. Using Account A, call `ModifyDBClusterSnapshotAttribute`, specifying **restore** for the `AttributeName` parameter, and the ID for Account B for the `ValuesToAdd` parameter\.

1. \(If the snapshot is encrypted\) Using Account A, update the key policy for the AWS KMS CMK, first adding the ARN of Account B as a `Principal`, and then allow the `kms:CreateGrant` action\.

1. \(If the snapshot is encrypted\) Using Account B, choose or create an IAM user and attach an IAM policy to that user that allows it to copy an encrypted DB cluster snapshot using your AWS KMS CMK\.

1. Using Account B, call `CopyDBClusterSnapshot` and use the `SourceDBClusterSnapshotIdentifier` parameter to specify the ARN of the DB cluster snapshot to be copied, which must include the ID for Account A\.

To list all of the AWS accounts permitted to restore a DB cluster snapshot, use the [ DescribeDBSnapshotAttributes](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBSnapshotAttributes.html) or [ DescribeDBClusterSnapshotAttributes](https://docs.aws.amazon.com/AmazonRDS/latest/APIReference/API_DescribeDBClusterSnapshotAttributes.html) API operation\.

To remove sharing permission for an AWS account, use the `ModifyDBSnapshotAttribute` or `ModifyDBClusterSnapshotAttribute` action with `AttributeName` set to `restore` and the ID of the account to remove in the `ValuesToRemove` parameter\.

#### Copying an Unencrypted DB Cluster Snapshot to Another Account<a name="USER_CopyDBClusterSnapshot.Unencrypted.CrossAccount"></a>

Use the following procedure to copy an unencrypted DB cluster snapshot to another account in the same AWS Region\.

1. In the source account for the DB cluster snapshot, call `ModifyDBClusterSnapshotAttribute`, specifying **restore** for the `AttributeName` parameter, and the ID for the target account for the `ValuesToAdd` parameter\.

   Running the following example using the account `987654321` permits two AWS account identifiers, `123451234512` and `123456789012`, to restore the DB cluster snapshot named `manual-snapshot1`\.

   ```
   https://rds.us-west-2.amazonaws.com/
   	?Action=ModifyDBClusterSnapshotAttribute
   	&AttributeName=restore
   	&DBClusterSnapshotIdentifier=manual-snapshot1
   	&SignatureMethod=HmacSHA256&SignatureVersion=4
   	&ValuesToAdd.member.1=123451234512
   	&ValuesToAdd.member.2=123456789012
   	&Version=2014-10-31
   	&X-Amz-Algorithm=AWS4-HMAC-SHA256
   	&X-Amz-Credential=AKIADQKE4SARGYLE/20150922/us-west-2/rds/aws4_request
   	&X-Amz-Date=20150922T220515Z
   	&X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
   	&X-Amz-Signature=ef38f1ce3dab4e1dbf113d8d2a265c67d17ece1999ffd36be85714ed36dddbb3
   ```

1. In the target account, call `CopyDBClusterSnapshot` and use the `SourceDBClusterSnapshotIdentifier` parameter to specify the ARN of the DB cluster snapshot to be copied, which must include the ID for the source account\.

   Running the following example using the account `123451234512` copies the DB cluster snapshot `aurora-cluster1-snapshot-20130805` from account `987654321` and creates a DB cluster snapshot named `dbclustersnapshot1`\.

   ```
   https://rds.us-west-2.amazonaws.com/
      ?Action=CopyDBClusterSnapshot
      &CopyTags=true
      &SignatureMethod=HmacSHA256
      &SignatureVersion=4
      &SourceDBClusterSnapshotIdentifier=arn:aws:rds:us-west-2:987654321:cluster-snapshot:aurora-cluster1-snapshot-20130805
      &TargetDBClusterSnapshotIdentifier=dbclustersnapshot1
      &Version=2013-09-09
      &X-Amz-Algorithm=AWS4-HMAC-SHA256
      &X-Amz-Credential=AKIADQKE4SARGYLE/20150922/us-west-2/rds/aws4_request
      &X-Amz-Date=20140429T175351Z
      &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
      &X-Amz-Signature=9164337efa99caf850e874a1cb7ef62f3cea29d0b448b9e0e7c53b288ddffed2
   ```

#### Copying an Encrypted DB Cluster Snapshot to Another Account<a name="USER_CopyDBClusterSnapshot.Encrypted.CrossAccount"></a>

Use the following procedure to copy an encrypted DB cluster snapshot to another account in the same AWS Region\.

1. In the source account for the DB cluster snapshot, call `ModifyDBClusterSnapshotAttribute`, specifying **restore** for the `AttributeName` parameter, and the ID for the target account for the `ValuesToAdd` parameter\.

   Running the following example using the account `987654321` permits two AWS account identifiers, `123451234512` and `123456789012`, to restore the DB cluster snapshot named `manual-snapshot1`\.

   ```
   https://rds.us-west-2.amazonaws.com/
   	?Action=ModifyDBClusterSnapshotAttribute
   	&AttributeName=restore
   	&DBClusterSnapshotIdentifier=manual-snapshot1
   	&SignatureMethod=HmacSHA256&SignatureVersion=4
   	&ValuesToAdd.member.1=123451234512
   	&ValuesToAdd.member.2=123456789012
   	&Version=2014-10-31
   	&X-Amz-Algorithm=AWS4-HMAC-SHA256
   	&X-Amz-Credential=AKIADQKE4SARGYLE/20150922/us-west-2/rds/aws4_request
   	&X-Amz-Date=20150922T220515Z
   	&X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
   	&X-Amz-Signature=ef38f1ce3dab4e1dbf113d8d2a265c67d17ece1999ffd36be85714ed36dddbb3
   ```

1. In the source account for the DB cluster snapshot, update the key policy for the AWS KMS CMK, first adding the ARN of the target account as a `Principal`, and then allow the `kms:CreateGrant` action\. For more information, see [Allowing Access to an AWS KMS Customer Master Key \(CMK\)](USER_ShareSnapshot.md#USER_ShareSnapshot.Encrypted.KeyPolicy)\.

1. In the target account, choose or create an IAM user and attach an IAM policy to that user that allows it to copy an encrypted DB cluster snapshot using your AWS KMS CMK\. For more information, see [Creating an IAM Policy to Enable Copying of the Encrypted Snapshot](USER_ShareSnapshot.md#USER_ShareSnapshot.Encrypted.KeyPolicy.IAM)\.

1. In the target account, call `CopyDBClusterSnapshot` and use the `SourceDBClusterSnapshotIdentifier` parameter to specify the ARN of the DB cluster snapshot to be copied, which must include the ID for the source account\.

   Running the following example using the account `123451234512` copies the DB cluster snapshot `aurora-cluster1-snapshot-20130805` from account `987654321` and creates a DB cluster snapshot named `dbclustersnapshot1`\.

   ```
   https://rds.us-west-2.amazonaws.com/
      ?Action=CopyDBClusterSnapshot
      &CopyTags=true
      &SignatureMethod=HmacSHA256
      &SignatureVersion=4
      &SourceDBClusterSnapshotIdentifier=arn:aws:rds:us-west-2:987654321:cluster-snapshot:aurora-cluster1-snapshot-20130805
      &TargetDBClusterSnapshotIdentifier=dbclustersnapshot1
      &Version=2013-09-09
      &X-Amz-Algorithm=AWS4-HMAC-SHA256
      &X-Amz-Credential=AKIADQKE4SARGYLE/20150922/us-west-2/rds/aws4_request
      &X-Amz-Date=20140429T175351Z
      &X-Amz-SignedHeaders=content-type;host;user-agent;x-amz-content-sha256;x-amz-date
      &X-Amz-Signature=9164337efa99caf850e874a1cb7ef62f3cea29d0b448b9e0e7c53b288ddffed2
   ```