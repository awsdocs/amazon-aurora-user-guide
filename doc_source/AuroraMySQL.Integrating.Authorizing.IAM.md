# Setting up IAM roles to access AWS services<a name="AuroraMySQL.Integrating.Authorizing.IAM"></a>

To permit your Aurora DB cluster to access another AWS service, do the following:

1. Create an IAM policy that grants permission to the AWS service\. For more information, see:
   + [Creating an IAM policy to access Amazon S3 resources](AuroraMySQL.Integrating.Authorizing.IAM.S3CreatePolicy.md)
   + [Creating an IAM policy to access AWS Lambda resources](AuroraMySQL.Integrating.Authorizing.IAM.LambdaCreatePolicy.md)
   + [Creating an IAM policy to access CloudWatch Logs resources](AuroraMySQL.Integrating.Authorizing.IAM.CWCreatePolicy.md)
   + [Creating an IAM policy to access AWS KMS resources](AuroraMySQL.Integrating.Authorizing.IAM.KMSCreatePolicy.md)

1. Create an IAM role and attach the policy that you created\. For more information, see [Creating an IAM role to allow Amazon Aurora to access AWS services](AuroraMySQL.Integrating.Authorizing.IAM.CreateRole.md)\.

1. Associate that IAM role with your Aurora DB cluster\. For more information, see [Associating an IAM role with an Amazon Aurora MySQL DB cluster](AuroraMySQL.Integrating.Authorizing.IAM.AddRoleToDBCluster.md)\.