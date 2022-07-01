# Amazon Aurora PostgreSQL reference<a name="AuroraPostgreSQL.Reference"></a>

**Topics**
+ [Aurora PostgreSQL collations for EBCDIC and other mainframe migrations](#AuroraPostgreSQL.Reference.Collations.mainframe.migration)
+ [Aurora PostgreSQL functions reference](Appendix.AuroraPostgreSQL.Functions.md)
+ [Amazon Aurora PostgreSQL parameters](AuroraPostgreSQL.Reference.ParameterGroups.md)
+ [Amazon Aurora PostgreSQL wait events](AuroraPostgreSQL.Reference.Waitevents.md)

## Aurora PostgreSQL collations for EBCDIC and other mainframe migrations<a name="AuroraPostgreSQL.Reference.Collations.mainframe.migration"></a>

Migrating mainframe applications to new platforms such as AWS ideally preserves application behavior\. To preserve application behavior on a new platform exactly as it was on the mainframe requires that migrated data be collated using the same collation and sorting rules\. For example, many Db2 migration solutions shift null values to u0180 \(Unicode position 0180\), so these collations sort u0180 first\. This is one example of how collations can vary from their mainframe source and why it's necessary to choose a collation that better maps to the original EBCDIC collation\. 

Aurora PostgreSQL 14\.3 and higher versions provide many ICU and EBCDIC collations to support such migration to AWS using the AWS Mainframe Modernization service\. To learn more about this service, see [What is AWS Mainframe Modernization?](https://docs.aws.amazon.com/m2/latest/userguide/what-is-m2.html) 

 In the following table, you can find Aurora PostgreSQL–provided collations\. These collations follow EBCDIC rules and ensure that mainframe applications function the same on AWS as they did in the mainframe environment\. The collation name includes the relevant code page, \(cp*nnnn*\), so that you can choose the appropriate collation for your mainframe source\. For example, use `en-US-cp037-x-icu` for to achieve the collation behavior for EBCDIC data that originated from a mainframe application that used code page 037\.


| EBCDIC collations  | AWS Blu Age collations | AWS Micro Focus collations | 
| --- | --- | --- | 
| da\-DK\-cp1142\-x\-icu | da\-DK\-cp1142b\-x\-icu | da\-DK\-cp1142m\-x\-icu | 
| da\-DK\-cp277\-x\-icu | da\-DK\-cp277b\-x\-icu | – | 
| de\-DE\-cp1141\-x\-icu | de\-DE\-cp1141b\-x\-icu | de\-DE\-cp1141m\-x\-icu | 
| de\-DE\-cp273\-x\-icu | de\-DE\-cp273b\-x\-icu | – | 
| en\-GB\-cp1146\-x\-icu | en\-GB\-cp1146b\-x\-icu | en\-GB\-cp1146m\-x\-icu | 
| en\-GB\-cp285\-x\-icu | en\-GB\-cp285b\-x\-icu | – | 
| en\-US\-cp037\-x\-icu | en\-US\-cp037b\-x\-icu | – | 
| en\-US\-cp1140\-x\-icu | en\-US\-cp1140b\-x\-icu | en\-US\-cp1140m\-x\-icu | 
| es\-ES\-cp1145\-x\-icu | es\-ES\-cp1145b\-x\-icu | es\-ES\-cp1145m\-x\-icu | 
| es\-ES\-cp284\-x\-icu | es\-ES\-cp284b\-x\-icu | – | 
| fi\-FI\-cp1143\-x\-icu | fi\-FI\-cp1143b\-x\-icu | fi\-FI\-cp1143m\-x\-icu | 
| fi\-FI\-cp278\-x\-icu | fi\-FI\-cp278b\-x\-icu | – | 
| fr\-FR\-cp1147\-x\-icu | fr\-FR\-cp1147b\-x\-icu | fr\-FR\-cp1147m\-x\-icu | 
| fr\-FR\-cp297\-x\-icu | fr\-FR\-cp297b\-x\-icu | – | 
| it\-IT\-cp1144\-x\-icu | it\-IT\-cp1144b\-x\-icu | it\-IT\-cp1144m\-x\-icu | 
| it\-IT\-cp280\-x\-icu | it\-IT\-cp280b\-x\-icu | – | 
| nl\-BE\-cp1148\-x\-icu | nl\-BE\-cp1148b\-x\-icu | nl\-BE\-cp1148m\-x\-icu | 
| nl\-BE\-cp500\-x\-icu | nl\-BE\-cp500b\-x\-icu | – | 

To learn more about AWS Blu Age, see [Tutorial: Managed Runtime for AWS Blu Age](https://docs.aws.amazon.com/m2/latest/userguide/tutorial-runtime-ba.html) in the *AWS Mainframe Modernization User Guide*\. 

For more information about working with AWS Micro Focus, see [Tutorial: Managed Runtime for Micro Focus](https://docs.aws.amazon.com/m2/latest/userguide/tutorial-runtime.html) in the *AWS Mainframe Modernization User Guide*\.

For more information about managing collations in PostgreSQL, see [Collation Support](https://www.postgresql.org/docs/current/collation.html) in the PostgreSQL documentation\.