# Monitoring threats with Amazon GuardDuty RDS Protection<a name="guard-duty-rds-protection"></a>


|  | 
| --- |
| This is preview documentation for Amazon GuardDuty RDS Protection\. It is subject to change\. | 

Amazon GuardDuty is a continuous security monitoring service that analyzes and processes various data sources, including RDS login activity\. It uses threat intelligence feeds and machine learning to identify unexpected, potentially unauthorized, and malicious activity within your AWS environment\.

GuardDuty RDS Protection analyzes and profiles login events for potential access threats to your Amazon Aurora databases\. When you turn on RDS Protection, GuardDuty consumes RDS login events from your Aurora databases\. RDS Protection monitors these events and profiles them for potential insider threats or external actors\. When RDS Protection detects a potential threat, such as an unusual pattern in successful or failed login attempts, GuardDuty generates a new finding with details about the potentially compromised database\.

You can turn the RDS Protection feature on or off for any AWS account in any AWS Region where this feature is available\. When RDS Protection isn't enabled, GuardDuty doesn't detect potentially compromised Aurora databases or provide details of the compromise\.

Amazon GuardDuty RDS Protection supports the following Aurora database versions:
+ Aurora MySQL version 2\.10\.2 and 3\.2\.1 and higher minor versions
+ Aurora PostgreSQL version 10\.17, 11\.12, 12\.7, 13\.3, and 14\.3 and higher minor versions

For more information, see [GuardDuty RDS Protection](https://docs.aws.amazon.com/guardduty/latest/ug/rds-protection.html) in the *Amazon GuardDuty User Guide*\.