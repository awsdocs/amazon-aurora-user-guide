# Creating an IAM Role to Allow Amazon Aurora to Access AWS Services<a name="AuroraMySQL.Integrating.Authorizing.IAM.CreateRole"></a>

After creating an IAM policy to allow Aurora to access AWS resources, you must create an IAM role and attach the IAM policy to the new IAM role\.

To create an IAM role to permit your Amazon RDS cluster to communicate with other AWS services on your behalf, take the following steps\.<a name="Create.IAMRole.AWSServices"></a>

**To create an IAM role to allow Amazon RDS to access AWS services**

1. Open the [IAM console](https://console.aws.amazon.com/iam/home?#home)\.

1. In the navigation pane, choose **Roles**\.

1. Choose **Create role**\.

1. Under **AWS service**, choose **RDS**\.

1. Under **Select your use case**, choose **RDS – Add Role to Database**\.

1. Choose **Next: Permissions**\.

1. On the **Attach permissions policies** page, enter the name of your policy in the **Search** field\.

1. When it appears in the list, select the policy that you defined earlier using the instructions in one of the following sections:
   + [Creating an IAM Policy to Access Amazon S3 Resources](AuroraMySQL.Integrating.Authorizing.IAM.S3CreatePolicy.md)
   + [Creating an IAM Policy to Access AWS Lambda Resources](AuroraMySQL.Integrating.Authorizing.IAM.LambdaCreatePolicy.md)
   + [Creating an IAM Policy to Access CloudWatch Logs Resources](AuroraMySQL.Integrating.Authorizing.IAM.CWCreatePolicy.md)
   + [Creating an IAM Policy to Access AWS KMS Resources](AuroraMySQL.Integrating.Authorizing.IAM.KMSCreatePolicy.md)

1. Choose **Next: Tags**, and then choose **Next: Review**\.

1. In **Role name**, enter a name for your IAM role, for example `RDSLoadFromS3`\. You can also add an optional **Role description** value\.

1. Choose **Create Role**\.

1. Complete the steps in [Associating an IAM Role with an Amazon Aurora MySQL DB Cluster](AuroraMySQL.Integrating.Authorizing.IAM.AddRoleToDBCluster.md)\.