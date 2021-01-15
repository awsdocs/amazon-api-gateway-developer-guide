# Getting started with API Gateway<a name="getting-started"></a>

In this getting started exercise, you create a serverless API\. Serverless APIs let you focus on your applications, instead of spending time provisioning and managing servers\. This exercise takes less than 20 minutes to complete, and is possible within the [AWS Free Tier](http://aws.amazon.com/free)\.

First, you create a Lambda function using the AWS Lambda console\. Next, you create an HTTP API using the API Gateway console\. Then, you invoke your API\.

When you invoke your HTTP API, API Gateway routes the request to your Lambda function\. Lambda runs the Lambda function and returns a response to API Gateway\. API Gateway then returns a response to you\.

![\[Architectural overview of the API that you create in this getting started guide. Clients use an API Gateway HTTP API to invoke a Lambda function. API Gateway returns the Lambda function's response to clients.\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/getting-started-overview.png)

To complete this exercise, you need an AWS account and an AWS Identity and Access Management user with console access\. More more information, see [Prerequisites for getting started with API Gateway](setting-up.md)\.

**Topics**
+ [Step 1: Create a Lambda function](#getting-started-create-function)
+ [Step 2: Create an HTTP API](#getting-started-create-api)
+ [Step 3: Test your API](#getting-started-invoke-api)
+ [\(Optional\) Step 4: Clean up](#getting-started-cleanup)
+ [Next steps](#getting-started-next-steps)

## Step 1: Create a Lambda function<a name="getting-started-create-function"></a>

You use a Lambda function for the backend of your API\. Lambda runs your code only when needed and scales automatically, from a few requests per day to thousands per second\.

For this example, you use the default Node\.js function from the Lambda console\.

**To create a Lambda function**

1. Sign in to the Lambda console at [https://console\.aws\.amazon\.com/lambda](https://console.aws.amazon.com/lambda)\.

1. Choose **Create function**\.

1. For **Function name**, enter **my\-function**\.

1. Choose **Create function**\.

The example function returns a `200` response to clients, and the text `Hello from Lambda!`\.

You can modify your Lambda function, as long as the function's response aligns with the [format that API Gateway requires](http-api-develop-integrations-lambda.md#http-api-develop-integrations-lambda.response)\.

The default Lambda function code should look similar to the following:

```
exports.handler = async (event) => {
    const response = {
        statusCode: 200,
        body: JSON.stringify('Hello from Lambda!'),
    };
    return response;
};
```

## Step 2: Create an HTTP API<a name="getting-started-create-api"></a>

Next, you create an HTTP API\. API Gateway also supports REST APIs and WebSocket APIs, but an HTTP API is the best choice for this exercise\. HTTP APIs have lower latency and lower cost than REST APIs\. WebSocket APIs maintain persistent connections with clients for full\-duplex communication, which isn't required for this example\.

The HTTP API provides an HTTP endpoint for your Lambda function\. API Gateway routes requests to your Lambda function, and then returns the function's response to clients\.



**To create an HTTP API**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Do one of the following:
   + To create your first API, for **HTTP API**, choose **Build**\.
   + If you've created an API before, choose **Create API**, and then choose **Build** for **HTTP API**\.

1. For **Integrations**, choose **Add integration**\.

1. Choose **Lambda**\.

1. For **Lambda function**, enter **my\-function**\.

1. For **API name**, enter **my\-http\-api**\.

1. Choose **Next**\.

1. Review the *route* that API Gateway creates for you, and then choose **Next**\.

1. Review the *stage* that API Gateway creates for you, and then choose **Next**\.

1. Choose **Create**\.

Now you've created an HTTP API with a Lambda integration that's ready to receive requests from clients\.

## Step 3: Test your API<a name="getting-started-invoke-api"></a>

Next, you test your API to make sure that it's working\. For simplicity, use a web browser to invoke your API\.

**To test your API**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Choose your API\.

1. Note your API's invoke URL\.  
![\[After you create your API, the console shows your API's invoke URL.\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/getting-started-invoke-url.png)

1. Copy your API's invoke URL, and enter it in a web browser\. Append the name of your Lambda function to your invoke URL to call your Lambda function\. By default, the API Gateway console creates a route with the same name as your Lambda function, `my-function`\. 

   The full URL should look like `https://abcdef123.execute-api.us-east-2.amazonaws.com/my-function`\. 

   Your browser sends a `GET` request to the API\.

1. Verify your API's response\. You should see the text `"Hello from Lambda!"` in your browser\.

## \(Optional\) Step 4: Clean up<a name="getting-started-cleanup"></a>

To prevent unnecessary costs, delete the resources that you created as part of this getting started exercise\. The following steps delete your HTTP API, your Lambda function, and associated resources\.

**To delete an HTTP API**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. On the **APIs** page, select an API\. Choose **Actions**, and then choose **Delete**\.

1. Choose **Delete**\.

**To delete a Lambda function**

1. Sign in to the Lambda console at [https://console\.aws\.amazon\.com/lambda](https://console.aws.amazon.com/lambda)\.

1. On the **Functions** page, select a function\. Choose **Actions**, and then choose **Delete**\.

1. Choose **Delete**\.

**To delete a Lambda function's log group**

1. In the Amazon CloudWatch console, open the [Log groups page](https://console.aws.amazon.com/cloudwatch/home#logs:)\.

1. On the **Log groups** page, select the function's log group \(`/aws/lambda/my-function`\)\. Choose **Actions**, and then choose **Delete log group**\.

1. Choose **Delete**\.

**To delete a Lambda function's execution role**

1. In the AWS Identity and Access Management console, open the [Roles page](https://console.aws.amazon.com/iam/home?#/roles)\.

1. Select the function's role, for example, `my-function-31exxmpl`\.

1. Choose **Delete role**\.

1. Choose **Yes, delete**\.

You can automate the creation and cleanup of AWS resources by using AWS CloudFormation or AWS SAM\. For example AWS CloudFormation templates, see [example AWS CloudFormation templates](https://github.com/awsdocs/amazon-api-gateway-developer-guide/tree/main/cloudformation-templates)\.

## Next steps<a name="getting-started-next-steps"></a>

For this example, you used the AWS Management Console to create a simple HTTP API\. The HTTP API invokes a Lambda function and returns a response to clients\.

The following are next steps as you continue to work with API Gateway\.
+ [Configure additional types of API integrations,](http-api-develop-integrations.md) including:
  + [HTTP endpoints](http-api-develop-integrations-http.md)
  + [Private resources in a VPC, such as Amazon ECS services](http-api-develop-integrations-private.md)
  + [AWS services such as Amazon Simple Queue Service, AWS Step Functions, and Kinesis Data Streams](http-api-develop-integrations-aws-services.md)
+ [Control access to your APIs](http-api-access-control.md)
+ [Enable logging for your APIs](http-api-logging.md)
+ [Configure throttling for your APIs](http-api-throttling.md)
+ [Configure custom domains for your APIs](http-api-custom-domain-names.md)

To get help with Amazon API Gateway from the community, see the [API Gateway Discussion Forum](http://forums.aws.amazon.com/forum.jspa?forumID=199)\. When you enter this forum, AWS might require you to sign in\.

To get help with API Gateway directly from AWS, see the support options on the [AWS Support page](http://aws.amazon.com/premiumsupport/)\.

See also our [frequently asked questions \(FAQs\)](http://aws.amazon.com/api-gateway/faqs/), or [contact us directly](http://aws.amazon.com/contact-us/)\.