# Monitoring threats with Amazon GuardDuty RDS Protection<a name="guard-duty-rds-protection"></a>

Amazon GuardDuty is a continuous security monitoring service that analyzes and processes various data sources, including RDS login activity\. It uses threat intelligence feeds and machine learning to identify unexpected, potentially unauthorized, and malicious activity within your AWS environment\.

GuardDuty RDS Protection analyzes and profiles login events for potential access threats to your Amazon Aurora databases\. When you turn on RDS Protection, GuardDuty consumes RDS login events from your Aurora databases\. RDS Protection monitors these events and profiles them for potential insider threats or external actors\.

For more information about enabling GuardDuty RDS Protection, see [GuardDuty RDS Protection](https://docs.aws.amazon.com/guardduty/latest/ug/rds-protection.html) in the *Amazon GuardDuty User Guide*\.

When RDS Protection detects a potential threat, such as an unusual pattern in successful or failed login attempts, GuardDuty generates a new finding with details about the potentially compromised database\. You can view the finding details in the finding summary section in the Amazon GuardDuty console\. The finding details vary based on the finding type\. The primary details, resource type and resource role, determine the kind of information available for any finding\. For more information about the commonly available details for findings and the finding types, see [Finding details](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_findings-summary.html) and [GuardDuty RDS Protection finding types](https://docs.aws.amazon.com/guardduty/latest/ug/findings-rds-protection.html) respectively in the *Amazon GuardDuty User Guide*\. 

You can turn the RDS Protection feature on or off for any AWS account in any AWS Region where this feature is available\. When RDS Protection isn't enabled, GuardDuty doesn't detect potentially compromised Aurora databases or provide details of the compromise\.

An existing GuardDuty account can enable RDS Protection with a 30\-day trial period\. For a new GuardDuty account, RDS Protection is already enabled and included in the 30\-day free trial period\. For more information, see [Estimating GuardDuty cost](https://docs.aws.amazon.com/guardduty/latest/ug/monitoring_costs.html) in the *Amazon GuardDuty User Guide*\.

For information about the AWS Regions where GuardDuty doesn't yet support RDS Protection, see [Region\-specific feature availability](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_regions.html#gd-regional-feature-availability) in the *Amazon GuardDuty User Guide*\.

The following table provides the Aurora database versions that GuardDuty RDS Protection supports:


****  

| Amazon Aurora DB engine | Supported engine versions | 
| --- | --- | 
| Aurora MySQL |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/guard-duty-rds-protection.html)  | 
| Aurora PostgreSQL |  [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/guard-duty-rds-protection.html)  | 