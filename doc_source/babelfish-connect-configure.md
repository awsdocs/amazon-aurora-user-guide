# Creating C\# or JDBC client connections Babelfish<a name="babelfish-connect-configure"></a>

In the following you can find some examples of using C\# and JDBC classes to connect to an Babelfish for Aurora PostgreSQL\. 

**Example : Using C\# code to connect to a DB cluster**  

```
string dataSource = 'babelfishServer_11_24';

//Create connection
connectionString = @"Data Source=" + dataSource
    +";Initial Catalog=your-DB-name"
    +";User ID=user-id;Password=password";

SqlConnection cnn = new SqlConnection(connectionString);
cnn.Open();
```

**Example : Using generic JDBC API classes and interfaces to connect to a DB cluster**  

```
String dbServer = 
   "database-babelfish.cluster-123abc456def.us-east-1-rds.amazonaws.com";
String connectionUrl = "jdbc:sqlserver://" + dbServer + ":1433;" +
    "databaseName=your-DB-name;user=user-id;password=password";

// Load the SQL Server JDBC driver and establish the connection.
System.out.print("Connecting Babelfish Server ... ");
Connection cnn = DriverManager.getConnection(connectionUrl);
```

**Example : Using SQL Server\-specific JDBC classes and interfaces to connect to a DB cluster**  

```
// Create datasource.
SQLServerDataSource ds = new SQLServerDataSource();
ds.setUser("user-id");
ds.setPassword("password");
String babelfishServer = 
   "database-babelfish.cluster-123abc456def.us-east-1-rds.amazonaws.com";

ds.setServerName(babelfishServer);
ds.setPortNumber(1433);
ds.setDatabaseName("your-DB-name");

Connection con = ds.getConnection();
```