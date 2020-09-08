# Using Machine Learning \(ML\) Capabilities with Amazon Aurora<a name="aurora-ml"></a>

 Following, you can find a description of how to use machine learning \(ML\) capabilities in your Aurora database applications\. This feature simplifies developing database applications that use the Amazon SageMaker and Amazon Comprehend services to perform predictions\. In ML terminology, these predictions are known as *inferences*\. 

 This feature is suitable for many kinds of quick predictions\. Examples include low\-latency, real\-time use cases such as fraud detection, ad targeting, and product recommendations\. The queries pass customer profile, shopping history, and product catalog data to an SageMaker model\. Then your application gets product recommendations returned as query results\. 

 To use this feature, it helps for your organization to already have the appropriate ML models, notebooks, and so on available in the Amazon machine learning services\. You can divide the database knowledge and ML knowledge among the members of your team\. The database developers can focus on the SQL and database side of your application\. The Aurora Machine Learning feature enables the application to use ML processing through the familiar database interface of stored function calls\. 

**Topics**
+ [Using Machine Learning \(ML\) with Aurora MySQL](mysql-ml.md)
+ [Using Machine Learning \(ML\) with Aurora PostgreSQL](postgresql-ml.md)