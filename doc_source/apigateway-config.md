# Monitoring API Gateway API configuration with AWS Config<a name="apigateway-config"></a>

You can use [AWS Config](https://aws.amazon.com/config/) to record configuration changes made to your API Gateway API resources and send notifications based on resource changes\. Maintaining a configuration change history for API Gateway resources is useful for operational troubleshooting, audit, and compliance use cases\.

AWS Config can track changes to:
+ **API stage configuration**, such as:
  + cache cluster settings
  + throttle settings
  + access log settings
  + the active deployment set on the stage
+ **API configuration**, such as:
  + endpoint configuration
  + version
  + protocol
  + tags

In addition, the AWS Config Rules feature enables you to define configuration rules and automatically detect, track, and alert violations to these rules\. By tracking changes to these resource configuration properties, you can also author change\-triggered AWS Config rules for your API Gateway resources, and test your resource configurations against best practices\.

You can enable AWS Config in your account by using the AWS Config console or the AWS CLI\. Select the resource types for which you want to track changes\. If you previously configured AWS Config to record all resource types, then these API Gateway resources will be automatically recorded in your account\. Support for Amazon API Gateway in AWS Config is available in all AWS public regions and AWS GovCloud \(US\)\. For the full list of supported Regions, see [Amazon API Gateway Endpoints and Quotas](https://docs.aws.amazon.com/general/latest/gr/apigateway.html) in the AWS General Reference\.

**Topics**
+ [Supported resource types](#apigateway-config-resources-rules)
+ [Setting up AWS Config](#apigateway-config-setup)
+ [Configuring AWS Config to record API Gateway resources](#apigateway-config-configuring)
+ [Viewing API Gateway configuration details in the AWS Config console](#apigateway-config-console)
+ [Evaluating API Gateway resources using AWS Config rules](#apigateway-config-rules)

## Supported resource types<a name="apigateway-config-resources-rules"></a>

The following API Gateway resource types are integrated with AWS Config and are documented in [AWS Config Supported AWS Resource Types and Resource Relationships](https://docs.aws.amazon.com/config/latest/developerguide/resource-config-reference.html):
+ `AWS::ApiGatewayV2::Api` \(WebSocket and HTTP API\)
+ `AWS::ApiGateway::RestApi` \(REST API\)
+ `AWS::ApiGatewayV2::Stage` \(WebSocket and HTTP API stage\)
+ `AWS::ApiGateway::Stage` \(REST API stage\)

For more information about AWS Config, see the [AWS Config Developer Guide](https://docs.aws.amazon.com/config/latest/developerguide/)\. For pricing information, see the [AWS Config pricing information page](https://aws.amazon.com/config/pricing/)\.

**Important**  
If you change any of the following API properties after the API is deployed, you *must* [redeploy](how-to-deploy-api.md) the API to propagate the changes\. Otherwise, you'll see the attribute changes in the AWS Config console, but the previous property settings will still be in effect; the API's runtime behavior will be unchanged\.  
**`AWS::ApiGateway::RestApi`** – `binaryMediaTypes`, `minimumCompressionSize`, `apiKeySource`
**`AWS::ApiGatewayV2::Api`** – `apiKeySelectionExpression`

## Setting up AWS Config<a name="apigateway-config-setup"></a>

To initially set up AWS Config, see the following topics in the [AWS Config Developer Guide](https://docs.aws.amazon.com/config/latest/developerguide/)\.
+ [Setting Up AWS Config with the Console](https://docs.aws.amazon.com/config/latest/developerguide/gs-console.html)
+ [Setting Up AWS Config with the AWS CLI](https://docs.aws.amazon.com/config/latest/developerguide/gs-cli.html)

## Configuring AWS Config to record API Gateway resources<a name="apigateway-config-configuring"></a>

By default, AWS Config records configuration changes for all supported types of regional resources that it discovers in the region in which your environment is running\. You can customize AWS Config to record changes only for specific resource types, or changes to global resources\.

To learn about regional vs\. global resources and learn how to customize your AWS Config configuration, see [Selecting which Resources AWS Config Records](https://docs.aws.amazon.com/config/latest/developerguide/select-resources.html)\.

## Viewing API Gateway configuration details in the AWS Config console<a name="apigateway-config-console"></a>

You can use the AWS Config console to look for API Gateway resources and get current and historical details about their configurations\. The following procedure shows how to find information about an API Gateway API\.

**To find an API Gateway resource in the AWS config console**

1. Open the [AWS Config console](https://console.aws.amazon.com/config)\.

1. Choose **Resources**\.

1. On the **Resource** inventory page, choose **Resources**\.

1. Open the **Resource type** menu, scroll to APIGateway or APIGatewayV2, and then choose one or more of the API Gateway resource types\.

1. Choose **Look up**\.

1. Choose a resource ID in the list of resources that AWS Config displays\. AWS Config displays configuration details and other information about the resource you selected\.

1. To see the full details of the recorded configuration, choose **View Details**\.

To learn more ways to find a resource and view information on this page, see [Viewing AWS Resource Configurations and History](https://docs.aws.amazon.com/config/latest/developerguide/view-manage-resource.html) in the AWS Config Developer Guide\.

## Evaluating API Gateway resources using AWS Config rules<a name="apigateway-config-rules"></a>

You can create AWS Config rules, which represent the ideal configuration settings for your API Gateway resources\. You can use predefined [AWS Config Managed Rules](https://docs.aws.amazon.com/config/latest/developerguide/managed-rules-by-aws-config.html), or define custom rules\. AWS Config continuously tracks changes to the configuration of your resources to determine whether those changes violate any of the conditions in your rules\. The AWS Config console shows the compliance status of your rules and resources\.

If a resource violates a rule and is flagged as noncompliant, AWS Config can alert you using an [Amazon Simple Notification Service Developer Guide](https://docs.aws.amazon.com/sns/latest/dg/) \(Amazon SNS\) topic\. To programmatically consume the data in these AWS Config alerts, use an Amazon Simple Queue Service \(Amazon SQS\) queue as the notification endpoint for the Amazon SNS topic\.

To learn more about setting up and using rules, see [Evaluating Resources with Rules](https://docs.aws.amazon.com/config/latest/developerguide/evaluate-config.html) in the [AWS Config Developer Guide](https://docs.aws.amazon.com/config/latest/developerguide/)\. 