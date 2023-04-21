# Babelfish limitations<a name="babelfish-limitations"></a>

 The following limitations currently apply to Babelfish for Aurora PostgreSQL: 
+  Babelfish currently doesn't support the following Aurora features: 
  +  AWS Identity and Access Management 
  +  Database Activity Streams \(DAS\) 
  +  Query plan management \(QPM\) 
  +  Salted challenge response authentication mechanism \(SCRAM\) 
+  Babelfish currently doesn't support the following PostgreSQL features: 
  +  pg\_dump/pg\_restore utility to move a database from one Aurora PostgreSQL DB cluster to another 
  +  pg\_stat\_statement extension 
+  Babelfish doesn't provide the following client driver API support: 
  +  API requests with the connection attributes related to Microsoft Distributed Transaction Coordinator \(MSDTC\) aren't supported\. These include XA calls by the SQLServerXAResource class in the SQL server JDBC driver\. 
  +  API requests with the connection attributes and methods related to connection pooling aren't supported\. Usually,Babelfish for Aurora PostgreSQL performs well when the connection attributes of the connection pooling are removed\. 
+ The open source [jTDS driver](https://github.com/milesibastos/jTDS/) that is designed as an alternative to the Microsoft JDBC driver is not supported\.