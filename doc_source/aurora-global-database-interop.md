# Using Aurora global databases with other AWS services<a name="aurora-global-database-interop"></a>

 In some cases, you access other AWS services in combination with an Aurora global database\. In these cases, you need the same privileges, external functions, and so on, in the corresponding AWS Regions for all the associated clusters\. Even though an Aurora cluster in a global database might start out as a read\-only replication target, you might later promote it to the primary cluster\. To prepare for that possibility, set up any necessary write privileges for other services ahead of time, for all Aurora clusters in the global database\. 

 The following list summarizes the actions to take for each AWS service\. 

**Invoking AWS Lambda functions**  
 For all the Aurora clusters that make up the Aurora global database, perform the procedures in [Invoking a Lambda function from an Amazon Aurora MySQL DB cluster](AuroraMySQL.Integrating.Lambda.md)\.   
 For each cluster in the Aurora global database, set the `aws_default_lambda_role` cluster parameter to the Amazon Resource Name \(ARN\) of the new IAM \(IAM\) role\.   
 To permit database users in an Aurora global database to invoke Lambda functions, associate the role that you created in [Creating an IAM role to allow Amazon Aurora to access AWS services](AuroraMySQL.Integrating.Authorizing.IAM.CreateRole.md) with each cluster in the Aurora global database\.   
 Configure each cluster in the Aurora global database to allow outbound connections to Lambda\. For instructions, see [Enabling network communication from Amazon Aurora MySQL to other AWS services](AuroraMySQL.Integrating.Authorizing.Network.md)\. 

**Loading data from Amazon S3**  
 For all the Aurora clusters that make up the Aurora global database, perform the procedures in [Loading data into an Amazon Aurora MySQL DB cluster from text files in an Amazon S3 bucket](AuroraMySQL.Integrating.LoadFromS3.md)\.   
 For each Aurora cluster in the global database, set either the `aurora_load_from_s3_role` or `aws_default_s3_role` DB cluster parameter to the Amazon Resource Name \(ARN\) of the new IAM role\. If an IAM role isn't specified for `aurora_load_from_s3_role`, Aurora uses the IAM role specified in `aws_default_s3_role`\.   
 To permit database users in an Aurora global database to access S3, associate the role that you created in [Creating an IAM role to allow Amazon Aurora to access AWS services](AuroraMySQL.Integrating.Authorizing.IAM.CreateRole.md) with each Aurora cluster in the global database\.   
 Configure each Aurora cluster in the global database to allow outbound connections to S3\. For instructions, see [Enabling network communication from Amazon Aurora MySQL to other AWS services](AuroraMySQL.Integrating.Authorizing.Network.md)\. 

**Saving queried data to Amazon S3**  
 For all the Aurora clusters that make up the Aurora global database, perform the procedures in [Saving data from an Amazon Aurora MySQL DB cluster into text files in an Amazon S3 bucket](AuroraMySQL.Integrating.SaveIntoS3.md)\.   
 For each Aurora cluster in the global database, set either the `aurora_select_into_s3_role` or `aws_default_s3_role` DB cluster parameter to the Amazon Resource Name \(ARN\) of the new IAM role\. If an IAM role isn't specified for `aurora_select_into_s3_role`, Aurora uses the IAM role specified in `aws_default_s3_role`\.   
 To permit database users in an Aurora global database to access S3, associate the role that you created in [Creating an IAM role to allow Amazon Aurora to access AWS services](AuroraMySQL.Integrating.Authorizing.IAM.CreateRole.md) with each Aurora cluster in the global database\.   
 Configure each Aurora cluster in the global database to allow outbound connections to S3\. For instructions, see [Enabling network communication from Amazon Aurora MySQL to other AWS services](AuroraMySQL.Integrating.Authorizing.Network.md)\. 