# Creating an IAM role to allow Amazon Aurora to access AWS services<a name="AuroraMySQL.Integrating.Authorizing.IAM.CreateRole"></a>

After creating an IAM policy to allow Aurora to access AWS resources, you must create an IAM role and attach the IAM policy to the new IAM role\.

To create an IAM role to permit your Amazon RDS cluster to communicate with other AWS services on your behalf, take the following steps\.<a name="Create.IAMRole.AWSServices"></a>

**To create an IAM role to allow Amazon RDS to access AWS services**

1. Open the [IAM console](https://console.aws.amazon.com/iam/home?#home)\.

1. In the navigation pane, choose **Roles**\.

1. Choose **Create role**\.

1. Under **AWS service**, choose **RDS**\.

1. Under **Select your use case**, choose **RDS – Add Role to Database**\.

1. Choose **Next**\.

1. On the **Permissions policies** page, enter the name of your policy in the **Search** field\.

1. When it appears in the list, select the policy that you defined earlier using the instructions in one of the following sections:
   + [Creating an IAM policy to access Amazon S3 resources](AuroraMySQL.Integrating.Authorizing.IAM.S3CreatePolicy.md)
   + [Creating an IAM policy to access AWS Lambda resources](AuroraMySQL.Integrating.Authorizing.IAM.LambdaCreatePolicy.md)
   + [Creating an IAM policy to access CloudWatch Logs resources](AuroraMySQL.Integrating.Authorizing.IAM.CWCreatePolicy.md)
   + [Creating an IAM policy to access AWS KMS resources](AuroraMySQL.Integrating.Authorizing.IAM.KMSCreatePolicy.md)

1. Choose **Next**\.

1. In **Role name**, enter a name for your IAM role, for example `RDSLoadFromS3`\. You can also add an optional **Description** value\.

1. Choose **Create Role**\.

1. Complete the steps in [Associating an IAM role with an Amazon Aurora MySQL DB cluster](AuroraMySQL.Integrating.Authorizing.IAM.AddRoleToDBCluster.md)\.