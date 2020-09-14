# Customer Master Key \(CMK\) Management<a name="Overview.Encryption.Keys"></a>

You can manage customer master keys \(CMKs\) used for Amazon Aurora encrypted DB clusters using the [AWS Key Management Service \(AWS KMS\)](https://docs.aws.amazon.com/kms/latest/developerguide/) in the [AWS KMS console](https://console.aws.amazon.com/kms)\. If you want full control over a CMK, then you must create a customer managed CMK\.

You can't delete, revoke, or rotate default CMKs provisioned by AWS KMS\. You can't share a snapshot that has been encrypted using the default AWS KMS CMK of the AWS account that shared the snapshot\.

You can view audit logs of every action taken with a customer managed CMK by using [AWS CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\.

**Important**  
When Aurora encounters a DB cluster encrypted by a CMK that Aurora doesn't have access to, Aurora puts the DB cluster into a terminal state\. In this state, the DB cluster is no longer available and the current state of the database can't be recovered\. To restore the DB cluster, you must re\-enable access to the CMK for Aurora, and then restore the DB cluster from a backup\.