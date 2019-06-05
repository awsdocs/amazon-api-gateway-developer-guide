# TUTORIAL: Build an API Gateway API with AWS Integration<a name="getting-started-aws-proxy"></a>

 Both the [TUTORIAL: Build a Hello World API with Lambda Proxy Integration](api-gateway-create-api-as-simple-proxy-for-lambda.md) and [Build an API Gateway API with Lambda Integration](getting-started-with-lambda-integration.md) topics describe how to create an API Gateway API to expose the integrated Lambda function\. In addition, you can create an API Gateway API to expose other AWS services, such as Amazon SNS, Amazon S3, Amazon Kinesis, and even AWS Lambda\. This is made possible by the `AWS` integration\. The Lambda integration or the Lambda proxy integration is a special case, where the Lambda function invocation is exposed through the API Gateway API\. 

 All AWS services support dedicated APIs to expose their features\. However, the application protocols or programming interfaces are likely to differ from service to service\. An API Gateway API with the `AWS` integration has the advantage of providing a consistent application protocol for your client to access different AWS services\. 

 In this walkthrough, we create an API to expose Amazon SNS\. For more examples of integrating an API with other AWS services, see [Tutorials](api-gateway-tutorials.md)\. 

 Unlike the Lambda proxy integration, there is no corresponding proxy integration for other AWS services\. Hence, an API method is integrated with a single AWS action\. For more flexibility, similar to the proxy integration, you can set up a Lambda proxy integration\. The Lambda function then parses and processes requests for other AWS actions\. 

 API Gateway does not retry when the endpoint times out\. The API caller must implement retry logic to handle endpoint timeouts\. 

 This walkthrough builds on the instructions and concepts in [Build an API Gateway API with Lambda Integration](getting-started-with-lambda-integration.md)\.If you have not yet completed that walkthrough, we suggest that you do it first\. 

**Topics**
+ [Prerequisites](#getting-started-aws-proxy-prerequisites)
+ [Step 1: Create the Resource](#getting-started-aws-proxy-add-resources)
+ [Step 2: Create the GET Method](#getting-started-aws-proxy-add-methods)
+ [Step 3: Create the AWS Service Proxy Execution Role](#getting-started-aws-proxy-add-roles)
+ [Step 4: Specify Method Settings and Test the Method](#getting-started-aws-proxy-set-methods)
+ [Step 5: Deploy the API](#getting-started-aws-proxy-deploy)
+ [Step 6: Test the API](#getting-started-aws-proxy-test)
+ [Step 7: Clean Up](#getting-started-aws-proxy-clean-up)

## Prerequisites<a name="getting-started-aws-proxy-prerequisites"></a>

Before you begin this walkthrough, do the following:

1. Complete the steps in [Prerequisites: Get Ready to Build an API in API Gateway](setting-up.md)\.

1. Ensure that the IAM user has access to create policies and roles in IAM\. You need to create an IAM policy and role in this walkthrough\.

1.  Create a new API named `MyDemoAPI`\. For more information, see [TUTORIAL: Build an API with HTTP Non\-Proxy Integration](api-gateway-create-api-step-by-step.md)\. 

1. Deploy the API at least once to a stage named `test`\. For more information, see [Deploy the API](getting-started-lambda-non-proxy-integration.md#getting-started-deploy-api) in [Build an API Gateway API with Lambda Integration](getting-started-with-lambda-integration.md)\.

1. Complete the rest of the steps in [Build an API Gateway API with Lambda Integration](getting-started-with-lambda-integration.md)\.

1. Create at least one topic in Amazon Simple Notification Service \(Amazon SNS\)\. You will use the deployed API to get a list of topics in Amazon SNS that are associated with your AWS account\. To learn how to create a topic in Amazon SNS, see [Create a Topic](https://docs.aws.amazon.com/sns/latest/dg/CreateTopic.html)\. \(You do not need to copy the topic ARN mentioned in step 5\.\)

## Step 1: Create the Resource<a name="getting-started-aws-proxy-add-resources"></a>

In this step, you create a resource that enables the AWS service proxy to interact with the AWS service\.

**To create the resource**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. If **MyDemoAPI** is displayed, choose **Resources**\.

1. In the **Resources** pane, choose the resource root, represented by a single forward slash \(`/`\), and then choose **Create Resource**\.

1. For **Resource Name**, type **MyDemoAWSProxy**, and then choose **Create Resource**\.

## Step 2: Create the GET Method<a name="getting-started-aws-proxy-add-methods"></a>

In this step, you create a GET method that enables the AWS service proxy to interact with the AWS service\.

**To create the GET method**

1. In the **Resources** pane, choose **/mydemoawsproxy**, and then choose **Create Method**\.

1. For the HTTP method, choose **GET**, and then save your choice\.

## Step 3: Create the AWS Service Proxy Execution Role<a name="getting-started-aws-proxy-add-roles"></a>

In this step, you create an IAM role that your AWS service proxy uses to interact with the AWS service\. We call this IAM role an AWS service proxy execution role\. Without this role, API Gateway cannot interact with the AWS service\. In later steps, you specify this role in the settings for the GET method you just created\.

**To create the AWS service proxy execution role and its policy**

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. Choose **Policies**\.

1. Do one of the following:
   + If the **Welcome to Managed Policies** page appears, choose **Get Started**, and then choose **Create Policy**\.
   + If a list of policies appears, choose **Create Policy**\.

1. Next to **Create Your Own Policy**, choose **Select**\.

1. For **Policy Name**, type a name for the policy \(for example, **APIGatewayAWSProxyExecPolicy**\)\.

1. For **Description**, type **Enables API Gateway to call AWS services**\.

1. For **Policy Document**, type the following, and then choose **Create Policy**\.

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Resource": [
           "*"
         ],
         "Action": [
           "sns:ListTopics"           
         ]       
       }    
     ]
   }
   ```

   This policy document allows the caller to get a list of the Amazon SNS topics for the AWS account\.

1. Choose **Roles**\.

1. Choose **Create Role**\.

1.  Choose **AWS Service** under **Select role type** and then choose **API Gateway**\.

1. Choose **Next: Permissions**\.

1.  Choose **Next: Review**\.

1. For **Role Name**, type a name for the execution role \(for example, **APIGatewayAWSProxyExecRole**\), optionally type a description for this role, and then choose **Create role**\.

1. In the **Roles** list, choose the role you just created\. You may need to scroll down the list\.

1. For the selected role, choose **Attach policy**\.

1.  Select the check box next to the policy you created earlier \(for example, **APIGatewayAWSProxyExecPolicy**\) and choose **Attach policy**\.

1. The role you just created has the following trust relationship that enables API Gateway assume to role for any actions permitted by the attached policies:

   ```
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Sid": "",
         "Effect": "Allow",
         "Principal": {
           "Service": "apigateway.amazonaws.com"
         },
         "Action": "sts:AssumeRole"
       }
     ]
   }
   ```

   For **Role ARN**, note of the Amazon Resource Name \(ARN\) for the execution role\. You need it later\. The ARN should look similar to: `arn:aws:iam::123456789012:role/APIGatewayAWSProxyExecRole`, where `123456789012` is your AWS account ID\.

## Step 4: Specify Method Settings and Test the Method<a name="getting-started-aws-proxy-set-methods"></a>

In this step, you specify the settings for the GET method so that it can interact with an AWS service through an AWS service proxy\. You then test the method\.

**To specify settings for the GET method and then test it**

1. In the API Gateway console, in the **Resources** pane for the API named `MyDemoAPI`, in **/mydemoawsproxy**, choose **GET**\.

1. In the **Setup** pane, for **Integration type**, choose **Show advanced**, and then choose **AWS Service Proxy**\.

1. For **AWS Region**, choose the name of the AWS Region where you want to get the Amazon SNS topics\.

1. For **AWS Service**, choose **SNS**\.

1. For **HTTP method**, choose **GET**\.

1. For **Action**, type **ListTopics**\.

1. For **Execution Role**, type the ARN for the execution role\.

1. Leave **Path Override** blank\.

1. Choose **Save**\.

1. In the **Method Execution** pane, in the **Client** box, choose **TEST**, and then choose **Test**\. If successful, **Response Body** displays a response similar to the following:

   ```
   {
     "ListTopicsResponse": {
       "ListTopicsResult": {
         "NextToken": null,
         "Topics": [
           {
             "TopicArn": "arn:aws:sns:us-east-1:80398EXAMPLE:MySNSTopic-1"
           },
           {
             "TopicArn": "arn:aws:sns:us-east-1:80398EXAMPLE:MySNSTopic-2"
           },
           ...
           {
             "TopicArn": "arn:aws:sns:us-east-1:80398EXAMPLE:MySNSTopic-N"
           }
         ]
       },
       "ResponseMetadata": {
         "RequestId": "abc1de23-45fa-6789-b0c1-d2e345fa6b78"
       }
     }
   }
   ```

## Step 5: Deploy the API<a name="getting-started-aws-proxy-deploy"></a>

In this step, you deploy the API so that you can call it from outside of the API Gateway console\.

**To deploy the API**

1. In the **Resources** pane, choose **Deploy API**\.

1. For **Deployment stage**, choose `test`\.

1. For **Deployment description**, type **Calling AWS service proxy walkthrough**\.

1. Choose **Deploy**\.

## Step 6: Test the API<a name="getting-started-aws-proxy-test"></a>

In this step, you go outside of the API Gateway console and use your AWS service proxy to interact with the Amazon SNS service\.

1. In the **Stage Editor** pane, next to **Invoke URL**, copy the URL to the clipboard\. It should look like this:

   ```
   https://my-api-id.execute-api.region-id.amazonaws.com/test
   ```

1. Paste the URL into the address box of a new browser tab\.

1. Append `/mydemoawsproxy` so that it looks like this:

   ```
   https://my-api-id.execute-api.region-id.amazonaws.com/test/mydemoawsproxy
   ```

   Browse to the URL\. Information similar to the following should be displayed:

   ```
   {"ListTopicsResponse":{"ListTopicsResult":{"NextToken": null,"Topics":[{"TopicArn": "arn:aws:sns:us-east-1:80398EXAMPLE:MySNSTopic-1"},{"TopicArn": "arn:aws:sns:us-east-1:80398EXAMPLE:MySNSTopic-2"},...{"TopicArn": "arn:aws:sns:us-east-1:80398EXAMPLE:MySNSTopic-N}]},"ResponseMetadata":{"RequestId":"abc1de23-45fa-6789-b0c1-d2e345fa6b78}}}
   ```

## Step 7: Clean Up<a name="getting-started-aws-proxy-clean-up"></a>

You can delete the IAM resources the AWS service proxy needs to work\.

**Warning**  
If you delete an IAM resource an AWS service proxy relies on, that AWS service proxy and any APIs that rely on it will no longer work\. Deleting an IAM resource cannot be undone\. If you want to use the IAM resource again, you must re\-create it\.

**To delete the associated IAM resources**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the **Details** area, choose **Roles**\.

1. Select **APIGatewayAWSProxyExecRole**, and then choose **Role Actions**, **Delete Role**\. When prompted, choose **Yes, Delete**\.

1. In the **Details** area, choose **Policies**\.

1. Select **APIGatewayAWSProxyExecPolicy**, and then choose **Policy Actions**, **Delete**\. When prompted, choose **Delete**\.

 You have reached the end of this walkthrough\. For more detailed discussions about creating API as an AWS service proxy, see [TUTORIAL: Create a REST API as an Amazon S3 Proxy in API Gateway](integrating-api-with-aws-services-s3.md), [TUTORIAL: Create a `Calc` REST API with Two AWS Service Integrations and and One Lambda Non\-Proxy Integration](integrating-api-with-aws-services-lambda.md), or [TUTORIAL: Create a REST API as an Amazon Kinesis Proxy in API Gateway](integrating-api-with-aws-services-kinesis.md)\. 