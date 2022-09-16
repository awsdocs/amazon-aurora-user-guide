# Aurora Kerberos authentication<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.KerberosAuthentication"></a>

By using Kerberos authentication with Aurora, you can support external authentication of database users using Kerberos and Microsoft Active Directory\. Using Kerberos and Active Directory provides the benefits of single sign\-on and centralized authentication of database users\. Kerberos and Active Directory are available with AWS Directory Service for Microsoft Active Directory, a feature of AWS Directory Service\. For more information, see [Kerberos authentication](database-authentication.md#kerberos-authentication)\.

**Topics**
+ [Kerberos authentication with Aurora MySQL](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.KerberosAuthentication.ams)
+ [Kerberos authentication with Aurora PostgreSQL](#Concepts.Aurora_Fea_Regions_DB-eng.Feature.KerberosAuthentication.apg)

## Kerberos authentication with Aurora MySQL<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.KerberosAuthentication.ams"></a>

Amazon Aurora doesn't currently support Kerberos authentication for Aurora MySQL\.

## Kerberos authentication with Aurora PostgreSQL<a name="Concepts.Aurora_Fea_Regions_DB-eng.Feature.KerberosAuthentication.apg"></a>

In the AWS Regions where it's available, Kerberos authentication is supported on all currently available Aurora PostgreSQL versions\. For details, see [Amazon Aurora PostgreSQL updates](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraPostgreSQLReleaseNotes/AuroraPostgreSQL.Updates.html) in the *Release Notes for Aurora PostgreSQL*\. 

Aurora PostgreSQL\-Compatible Edition supports Kerberos authentication for Aurora PostgreSQL DB clusters in the following AWS Regions:
+ US East \(Ohio\)
+ US East \(N\. Virginia\)
+ US West \(N\. California\)
+ US West \(Oregon\)
+ Asia Pacific \(Mumbai\)
+ Asia Pacific \(Seoul\)
+ Asia Pacific \(Singapore\)
+ Asia Pacific \(Sydney\)
+ Asia Pacific \(Tokyo\)
+ Canada \(Central\)
+ China \(Beijing\)
+ China \(Ningxia\)
+ Europe \(Frankfurt\)
+ Europe \(Ireland\)
+ Europe \(London\)
+ Europe \(Paris\)
+ Europe \(Stockholm\)
+ South America \(São Paulo\)