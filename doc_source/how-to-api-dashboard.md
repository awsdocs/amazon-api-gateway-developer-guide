# View CloudWatch metrics with the API dashboard in API Gateway<a name="how-to-api-dashboard"></a>

You can use the API dashboard in the API Gateway Console to display the CloudWatch metrics of your deployed API in API Gateway\. These are shown as a summary of API activity over time\.  

**Topics**
+ [Prerequisites](#how-to-api-dashboard-prerequisites)
+ [Examine API activities in the dashboard](#how-to-api-dashboard-console)

## Prerequisites<a name="how-to-api-dashboard-prerequisites"></a>

1. You must have an API created in API Gateway\. Follow the instructions in [Creating a REST API in Amazon API Gateway](how-to-create-api.md)\.

1. You must have the API deployed at least once\. Follow the instructions in [Deploying a REST API in Amazon API Gateway](how-to-deploy-api.md)\.

1. To get CloudWatch metrics for individual methods, you must have CloudWatch Logs enabled for those methods in a given stage as described in [Update stage settings](stages.md#how-to-stage-settings)\. Your account will be charged for accessing method\-level logs, but not for accessing API\- or stage\-level logs\.

## Examine API activities in the dashboard<a name="how-to-api-dashboard-console"></a>

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Choose the name of the API\.

1. Under the selected API, choose **Dashboard**\.

1. To display a summary of API activity over time, for **Stage**, choose the desired stage\.

1. Use **From** and **To** to enter the date range\.

1. Refresh, if needed, and view individual metrics displayed in separate graphs titled **API Calls**, **Integration Latency**, **Latency**, **4xx Error** and **5xx Error**\. The **CacheHitCount** and **CacheMissCount** graphs will be displayed only if API caching has been enabled\.
**Tip**  
To examine method\-level CloudWatch metrics, make sure that you have enabled CloudWatch Logs on a method level\. For more information about how to set up method\-level logging, see [Update stage settings using the API Gateway console](stages.md#how-to-stage-settings-console)\.