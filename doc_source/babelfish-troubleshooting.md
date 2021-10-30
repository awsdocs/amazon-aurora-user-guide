# Babelfish troubleshooting<a name="babelfish-troubleshooting"></a>

Common causes of connection failures to a new Aurora DB cluster with Babelfish include the following:
+ **Security group doesn't allow access** – If you're having trouble connecting to a Babelfish, make sure that you added your IP address to the default Amazon EC2 security group\. You can use [https://checkip\.amazonaws\.com/](https://checkip.amazonaws.com/) to determine your IP address and then add it to your in\-bound rule for the TDS port and the PostgreSQL port\. For more information, see [Add rules to a security group](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/working-with-security-groups.html#adding-security-group-rule.html) in the *Amazon EC2 User Guide*\.

For more information about troubleshooting Aurora connection issues, see [Can't connect to Amazon RDS DB instance](CHAP_Troubleshooting.md#CHAP_Troubleshooting.Connecting)\. 