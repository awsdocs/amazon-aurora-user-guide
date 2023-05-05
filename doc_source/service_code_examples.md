# Code examples for Aurora using AWS SDKs<a name="service_code_examples"></a>

The following code examples show how to use Aurora with an AWS software development kit \(SDK\)\. 

*Actions* are code excerpts that show you how to call individual service functions\.

*Scenarios* are code examples that show you how to accomplish a specific task by calling multiple functions within the same service\.

*Cross\-service examples* are sample applications that work across multiple AWS services\.

For a complete list of AWS SDK developer guides and code examples, see [Using this service with an AWS SDK](CHAP_Tutorials.md#sdk-general-information-section)\. This topic also includes information about getting started and details about previous SDK versions\.

**Get started**

## Hello Aurora<a name="example_aurora_Hello_section"></a>

The following code example shows how to get started using Aurora\.

------
#### [ \.NET ]

**AWS SDK for \.NET**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/dotnetv3/Aurora#code-examples)\. 
  

```
using Amazon.RDS;
using Amazon.RDS.Model;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.DependencyInjection;

namespace AuroraActions;

public static class HelloAurora
{
    static async Task Main(string[] args)
    {
        // Use the AWS .NET Core Setup package to set up dependency injection for the
        // Amazon Relational Database Service (Amazon RDS).
        // Use your AWS profile name, or leave it blank to use the default profile.
        using var host = Host.CreateDefaultBuilder(args)
            .ConfigureServices((_, services) =>
                services.AddAWSService<IAmazonRDS>()
            ).Build();

        // Now the client is available for injection. Fetching it directly here for example purposes only.
        var rdsClient = host.Services.GetRequiredService<IAmazonRDS>();

        // You can use await and any of the async methods to get a response.
        var response = await rdsClient.DescribeDBClustersAsync(new DescribeDBClustersRequest { IncludeShared = true });
        Console.WriteLine($"Hello Amazon RDS Aurora! Let's list some clusters in this account:");
        foreach (var cluster in response.DBClusters)
        {
            Console.WriteLine($"\tCluster: database: {cluster.DatabaseName} identifier: {cluster.DBClusterIdentifier}.");
        }
    }
}
```
+  For API details, see [DescribeDBClusters](https://docs.aws.amazon.com/goto/DotNetSDKV3/rds-2014-10-31/DescribeDBClusters) in *AWS SDK for \.NET API Reference*\. 

------

**Contents**
+ [Actions](service_code_examples_actions.md)
  + [Create a DB cluster](example_aurora_CreateDBCluster_section.md)
  + [Create a DB cluster parameter group](example_aurora_CreateDBClusterParameterGroup_section.md)
  + [Create a DB cluster snapshot](example_aurora_CreateDBClusterSnapshot_section.md)
  + [Create a DB instance in a DB cluster](example_aurora_CreateDBInstance_section.md)
  + [Delete a DB cluster](example_aurora_DeleteDBCluster_section.md)
  + [Delete a DB cluster parameter group](example_aurora_DeleteDBClusterParameterGroup_section.md)
  + [Delete a DB instance](example_aurora_DeleteDBInstance_section.md)
  + [Describe DB cluster parameter groups](example_aurora_DescribeDBClusterParameterGroups_section.md)
  + [Describe DB cluster snapshots](example_aurora_DescribeDBClusterSnapshots_section.md)
  + [Describe DB clusters](example_aurora_DescribeDBClusters_section.md)
  + [Describe DB instances](example_aurora_DescribeDBInstances_section.md)
  + [Describe database engine versions](example_aurora_DescribeDBEngineVersions_section.md)
  + [Describe options for DB instances](example_aurora_DescribeOrderableDBInstanceOptions_section.md)
  + [Describe parameters from a DB cluster parameter group](example_aurora_DescribeDBClusterParameters_section.md)
  + [Update parameters in a DB cluster parameter group](example_aurora_ModifyDBClusterParameterGroup_section.md)
+ [Scenarios](service_code_examples_scenarios.md)
  + [Get started with DB clusters](example_aurora_Scenario_GetStartedClusters_section.md)
+ [Cross\-service examples](service_code_examples_cross-service_examples.md)
  + [Create a lending library REST API](example_cross_AuroraRestLendingLibrary_section.md)
  + [Create an Aurora Serverless work item tracker](example_cross_RDSDataTracker_section.md)