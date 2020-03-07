# Kerberos Authentication<a name="kerberos-authentication"></a>

Amazon Aurora supports external authentication of database users using Kerberos and Microsoft Active Directory\. Kerberos is a network authentication protocol that uses tickets and symmetric\-key cryptography to eliminate the need to transmit passwords over the network\. Kerberos has been built into Active Directory and is designed to authenticate users to network resources, such as databases\.

Amazon Aurora support for Kerberos and Active Directory provides the benefits of single sign\-on and centralized authentication of database users\. You can keep your user credentials in Active Directory\. Active Directory provides a centralized place for storing and managing credentials for multiple DB instances\.

You can enable your database users to authenticate against DB instances in two ways\. They can use credentials stored either in AWS Directory Service for Microsoft Active Directory or in your on\-premises Active Directory\.

Currently, Aurora supports Kerberos authentication for PostgreSQL DB clusters\. For more information, see [Using Kerberos Authentication with Aurora PostgreSQL](postgresql-kerberos.md)\.