# Babelfish limitations<a name="babelfish-limitations"></a>

 The following limitations currently apply to Babelfish for Aurora PostgreSQL: 
+  Babelfish currently don't support the following Aurora features: 
  +  AWS Directory Service 
  +  AWS Identity and Access Management 
  +  Database Activity Streams \(DAS\) 
  +  Kerberos 
  +  Query plan management \(QPM\) 
  +  Salted challenge response authentication mechanism \(SCRAM\) 
  +  Zero\-downtime patching \(ZDP\) 
+  Babelfish doesn't provide the following client driver API support: 
  +  API requests with the connection attributes related to Microsoft Distributed Transaction Coordinator \(MSDTC\) aren't supported\. These include XA calls by the SQLServerXAResource class in the SQL server JDBC driver\. 
  +  API requests with the connection attributes and methods related to connection pooling aren't supported\. Usually, Babelfish for Aurora PostgreSQL performs well when the connection attributes of the connection pooling are removed\. 
  +  API requests with the connection attributes related to the open source jTDS drivers that acts as an alternative for Microsoft JDBC driver aren't supported\. 
+  The deprecated SqlMembershipProvider that manages the storage of membership information \(session, roles, and user profiles\) isn't currently supported by Babelfish\. Microsoft recommends ASP\.NET Universal provider that provides a more secure default hashed password as an alternative to this API\. 