# Install a web server on your EC2 instance<a name="CHAP_Tutorials.WebServerDB.CreateWebServer"></a>

Install a web server on the EC2 instance you created in [Launch an EC2 instance](CHAP_Tutorials.WebServerDB.LaunchEC2.md)\. The web server connects to the Amazon Aurora DB cluster that you created in [Create an Amazon Aurora DB cluster](CHAP_Tutorials.WebServerDB.CreateDBCluster.md)\. 

## Install an Apache web server with PHP and MariaDB<a name="CHAP_Tutorials.WebServerDB.CreateWebServer.Apache"></a>

Connect to your EC2 instance and install the web server\.

**To connect to your EC2 instance and install the Apache web server with PHP**

1. Connect to the EC2 instance that you created earlier by following the steps in [Connect to your Linux instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html) in the *Amazon EC2 User Guide for Linux Instances*\.

   We recommend that you connect to your EC2 instance using SSH\. If the SSH client utility is installed on Windows, Linux, or Mac, you can connect to the instance using the following command format:

   ```
   ssh -i location_of_pem_file ec2-user@ec2-instance-public-dns-name
   ```

   For example, assume that `ec2-database-connect-key-pair.pem` is stored in `/dir1` on Linux, and the public IPv4 DNS for your EC2 instance is `ec2-12-345-678-90.compute-1.amazonaws.com`\. Your SSH command would look as follows:

   ```
   ssh -i /dir1/ec2-database-connect-key-pair.pem ec2-user@ec2-12-345-678-90.compute-1.amazonaws.com
   ```

1. Get the latest bug fixes and security updates by updating the software on your EC2 instance\. To do this, use the following command\.
**Note**  
The `-y` option installs the updates without asking for confirmation\. To examine updates before installing, omit this option\.

   ```
   sudo dnf update -y
   ```

1. After the updates complete, install the Apache web server, PHP, and MariaDB software using the following command\. This command installs multiple software packages and related dependencies at the same time\.

   ```
   sudo dnf install -y httpd php php-mysqli mariadb105
   ```

   If you receive an error, your instance probably wasn't launched with an Amazon Linux 2023 AMI\. You might be using the Amazon Linux 2 AMI instead\. You can view your version of Amazon Linux using the following command\.

   ```
   cat /etc/system-release
   ```

   For more information, see [Updating instance software](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/install-updates.html)\.

1. Start the web server with the command shown following\.

   ```
   sudo systemctl start httpd
   ```

   You can test that your web server is properly installed and started\. To do this, enter the public Domain Name System \(DNS\) name of your EC2 instance in the address bar of a web browser, for example: `http://ec2-42-8-168-21.us-west-1.compute.amazonaws.com`\. If your web server is running, then you see the Apache test page\. 

   If you don't see the Apache test page, check your inbound rules for the VPC security group that you created in [Tutorial: Create a VPC for use with a DB cluster \(IPv4 only\)](CHAP_Tutorials.WebServerDB.CreateVPC.md)\. Make sure that your inbound rules include one allowing HTTP \(port 80\) access for the IP address to connect to the web server\.
**Note**  
The Apache test page appears only when there is no content in the document root directory, `/var/www/html`\. After you add content to the document root directory, your content appears at the public DNS address of your EC2 instance\. Before this point, it appears on the Apache test page\.

1. Configure the web server to start with each system boot using the `systemctl` command\.

   ```
   sudo systemctl enable httpd
   ```

To allow `ec2-user` to manage files in the default root directory for your Apache web server, modify the ownership and permissions of the `/var/www` directory\. There are many ways to accomplish this task\. In this tutorial, you add `ec2-user` to the `apache` group, to give the `apache` group ownership of the `/var/www` directory and assign write permissions to the group\.

**To set file permissions for the Apache web server**

1. Add the `ec2-user` user to the `apache` group\.

   ```
   sudo usermod -a -G apache ec2-user
   ```

1. Log out to refresh your permissions and include the new `apache` group\.

   ```
   exit
   ```

1. Log back in again and verify that the `apache` group exists with the `groups` command\.

   ```
   groups
   ```

   Your output looks similar to the following:

   ```
   ec2-user adm wheel apache systemd-journal
   ```

1. Change the group ownership of the `/var/www` directory and its contents to the `apache` group\.

   ```
   sudo chown -R ec2-user:apache /var/www
   ```

1. Change the directory permissions of `/var/www` and its subdirectories to add group write permissions and set the group ID on subdirectories created in the future\.

   ```
   sudo chmod 2775 /var/www
   find /var/www -type d -exec sudo chmod 2775 {} \;
   ```

1. Recursively change the permissions for files in the `/var/www` directory and its subdirectories to add group write permissions\.

   ```
   find /var/www -type f -exec sudo chmod 0664 {} \;
   ```

Now, `ec2-user` \(and any future members of the `apache` group\) can add, delete, and edit files in the Apache document root\. This makes it possible for you to add content, such as a static website or a PHP application\. 

**Note**  
A web server running the HTTP protocol provides no transport security for the data that it sends or receives\. When you connect to an HTTP server using a web browser, much information is visible to eavesdroppers anywhere along the network pathway\. This information includes the URLs that you visit, the content of web pages that you receive, and the contents \(including passwords\) of any HTML forms\.   
The best practice for securing your web server is to install support for HTTPS \(HTTP Secure\)\. This protocol protects your data with SSL/TLS encryption\. For more information, see [ Tutorial: Configure SSL/TLS with the Amazon Linux AMI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/SSL-on-amazon-linux-ami.html) in the *Amazon EC2 User Guide*\.

## Connect your Apache web server to your DB cluster<a name="CHAP_Tutorials.WebServerDB.CreateWebServer.PHPContent"></a>

Next, you add content to your Apache web server that connects to your Amazon Aurora DB cluster\.

**To add content to the Apache web server that connects to your DB cluster**

1. While still connected to your EC2 instance, change the directory to `/var/www` and create a new subdirectory named `inc`\.

   ```
   cd /var/www
   mkdir inc
   cd inc
   ```

1. Create a new file in the `inc` directory named `dbinfo.inc`, and then edit the file by calling nano \(or the editor of your choice\)\.

   ```
   >dbinfo.inc
   nano dbinfo.inc
   ```

1. Add the following contents to the `dbinfo.inc` file\. Here, *db\_instance\_endpoint* is DB cluster writer endpoint, without the port, and *master password* is the master password for your DB cluster\.
**Note**  
We recommend placing the user name and password information in a folder that isn't part of the document root for your web server\. Doing this reduces the possibility of your security information being exposed\.

   ```
   <?php
   
   define('DB_SERVER', 'db_cluster_writer_endpoint');
   define('DB_USERNAME', 'tutorial_user');
   define('DB_PASSWORD', 'master password');
   define('DB_DATABASE', 'sample');
   
   ?>
   ```

1. Save and close the `dbinfo.inc` file\. If you are using nano, save and close the file by using Ctrl\+S and Ctrl\+X\.

1. Change the directory to `/var/www/html`\.

   ```
   cd /var/www/html
   ```

1. Create a new file in the `html` directory named `SamplePage.php`, and then edit the file by calling nano \(or the editor of your choice\)\.

   ```
   >SamplePage.php
   nano SamplePage.php
   ```

1. Add the following contents to the `SamplePage.php` file:

   ```
   <?php include "../inc/dbinfo.inc"; ?>
   <html>
   <body>
   <h1>Sample page</h1>
   <?php
   
     /* Connect to MySQL and select the database. */
     $connection = mysqli_connect(DB_SERVER, DB_USERNAME, DB_PASSWORD);
   
     if (mysqli_connect_errno()) echo "Failed to connect to MySQL: " . mysqli_connect_error();
   
     $database = mysqli_select_db($connection, DB_DATABASE);
   
     /* Ensure that the EMPLOYEES table exists. */
     VerifyEmployeesTable($connection, DB_DATABASE);
   
     /* If input fields are populated, add a row to the EMPLOYEES table. */
     $employee_name = htmlentities($_POST['NAME']);
     $employee_address = htmlentities($_POST['ADDRESS']);
   
     if (strlen($employee_name) || strlen($employee_address)) {
       AddEmployee($connection, $employee_name, $employee_address);
     }
   ?>
   
   <!-- Input form -->
   <form action="<?PHP echo $_SERVER['SCRIPT_NAME'] ?>" method="POST">
     <table border="0">
       <tr>
         <td>NAME</td>
         <td>ADDRESS</td>
       </tr>
       <tr>
         <td>
           <input type="text" name="NAME" maxlength="45" size="30" />
         </td>
         <td>
           <input type="text" name="ADDRESS" maxlength="90" size="60" />
         </td>
         <td>
           <input type="submit" value="Add Data" />
         </td>
       </tr>
     </table>
   </form>
   
   <!-- Display table data. -->
   <table border="1" cellpadding="2" cellspacing="2">
     <tr>
       <td>ID</td>
       <td>NAME</td>
       <td>ADDRESS</td>
     </tr>
   
   <?php
   
   $result = mysqli_query($connection, "SELECT * FROM EMPLOYEES");
   
   while($query_data = mysqli_fetch_row($result)) {
     echo "<tr>";
     echo "<td>",$query_data[0], "</td>",
          "<td>",$query_data[1], "</td>",
          "<td>",$query_data[2], "</td>";
     echo "</tr>";
   }
   ?>
   
   </table>
   
   <!-- Clean up. -->
   <?php
   
     mysqli_free_result($result);
     mysqli_close($connection);
   
   ?>
   
   </body>
   </html>
   
   
   <?php
   
   /* Add an employee to the table. */
   function AddEmployee($connection, $name, $address) {
      $n = mysqli_real_escape_string($connection, $name);
      $a = mysqli_real_escape_string($connection, $address);
   
      $query = "INSERT INTO EMPLOYEES (NAME, ADDRESS) VALUES ('$n', '$a');";
   
      if(!mysqli_query($connection, $query)) echo("<p>Error adding employee data.</p>");
   }
   
   /* Check whether the table exists and, if not, create it. */
   function VerifyEmployeesTable($connection, $dbName) {
     if(!TableExists("EMPLOYEES", $connection, $dbName))
     {
        $query = "CREATE TABLE EMPLOYEES (
            ID int(11) UNSIGNED AUTO_INCREMENT PRIMARY KEY,
            NAME VARCHAR(45),
            ADDRESS VARCHAR(90)
          )";
   
        if(!mysqli_query($connection, $query)) echo("<p>Error creating table.</p>");
     }
   }
   
   /* Check for the existence of a table. */
   function TableExists($tableName, $connection, $dbName) {
     $t = mysqli_real_escape_string($connection, $tableName);
     $d = mysqli_real_escape_string($connection, $dbName);
   
     $checktable = mysqli_query($connection,
         "SELECT TABLE_NAME FROM information_schema.TABLES WHERE TABLE_NAME = '$t' AND TABLE_SCHEMA = '$d'");
   
     if(mysqli_num_rows($checktable) > 0) return true;
   
     return false;
   }
   ?>
   ```

1. Save and close the `SamplePage.php` file\.

1. Verify that your web server successfully connects to your DB cluster by opening a web browser and browsing to `http://EC2 instance endpoint/SamplePage.php`, for example: `http://ec2-55-122-41-31.us-west-2.compute.amazonaws.com/SamplePage.php`\.

You can use `SamplePage.php` to add data to your DB cluster\. The data that you add is then displayed on the page\. To verify that the data was inserted into the table, install MySQL client on the Amazon EC2 instance\. Then connect to the DB cluster and query the table\. 

For information about connecting to a DB cluster, see [Connecting to an Amazon Aurora DB cluster](Aurora.Connecting.md)\.

To make sure that your DB cluster is as secure as possible, verify that sources outside of the VPC can't connect to your DB cluster\. 

After you have finished testing your web server and your database, you should delete your DB cluster and your Amazon EC2 instance\.
+ To delete a DB cluster, follow the instructions in [Deleting Aurora DB clusters and DB instances](USER_DeleteCluster.md)\. You don't need to create a final snapshot\.
+ To terminate an Amazon EC2 instance, follow the instruction in [Terminate your instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html) in the *Amazon EC2 User Guide*\.