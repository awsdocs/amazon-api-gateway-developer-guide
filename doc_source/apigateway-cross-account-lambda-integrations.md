# TUTORIAL: Build an API Gateway API with Cross\-Account Lambda Proxy Integration<a name="apigateway-cross-account-lambda-integrations"></a>

You can now use an AWS Lambda function from a different AWS account as your API integration backend\. Each account can be in any region where Amazon API Gateway is available\. This makes it easy to centrally manage and share Lambda backend functions across multiple APIs\.

In this section, we show how to configure cross\-account Lambda proxy integration using the Amazon API Gateway console\.

First, we create the example API from [TUTORIAL: Create a REST API by Importing an Example](api-gateway-create-api-from-example.md) in one account\. We then create a Lambda function in another account\. Finally, we use cross\-account Lambda integration to allow the example API to use the Lambda function we created in the second account\.

## Create API for API Gateway Cross\-Account Lambda Integration<a name="apigateway-cross-account-lambda-integrations-create-api"></a>

First, you'll create the example API as described in [TUTORIAL: Create a REST API by Importing an Example](api-gateway-create-api-from-example.md)\.

**To create the example API**

1.  Sign in to the API Gateway console\. 

1. Choose **Create API** from the API Gateway **APIs** home page: 

1. Under **Create new API**, choose **Examples API**\.

1. For **Endpoint Type**, choose **Edge optimized**\.

1. Choose **Import** to create the example API\.

## Create Lambda Integration Function in Another Account<a name="apigateway-cross-account-lambda-integrations-create-lambda-function"></a>

Now you'll create a Lambda function in a different account from the one in which you created the example API\.

**Creating a Lambda function in another account**

1. Log in to the Lambda console in a different account from the one where you created your API Gateway API\.

1. Choose **Create function**\.

1. Choose **Author from scratch**\.

1. Under **Author from scratch**, do the following:

   1. For **Function name**, enter a name\.

   1. From the **Runtime** drop\-down list, choose a supported Node\.js runtime\.

   1. Under **Permissions**, expand **Choose or create an execution role**\. You can create a role or choose an existing role\.

   1. Choose **Create function** to continue\.

1. Scroll down to the **Function code** pane\.

1. Copy\-paste the Node\.js function implementation from [TUTORIAL: Build a Hello World API with Lambda Proxy Integration](api-gateway-create-api-as-simple-proxy-for-lambda.md)\.

1. Choose **Save**\.

1. Note the full ARN for your function \(in the upper right corner of the Lambda function pane\)\. You'll need it when you create your cross\-account Lambda integration\.

## Configure Cross\-Account Lambda Integration<a name="apigateway-cross-account-lambda-integrations-create-integration2"></a>

Once you have a Lambda integration function in a different account, you can use the API Gateway console to add it to your API in your first account\.

**Note**  
If you are configuring a cross\-region, cross\-account authorizer, the `sourceArn` that is added to the target function should use the region of the function, not the region of the API\.

**Configuring your cross\-account Lambda integration**

1. In the API Gateway console, choose your API\.

1. Choose **Resources**\.

1. In the **Resources** pane, choose the top\-level `GET` method\. 

1. In the **Method Execution** pane, choose **Integration Request**\.

1. For **Integration type**, choose **Lambda Function**\.

1. Check **Use Lambda Proxy integration**\.

1. Leave **Lambda Region** set to your account's region\.

1. For **Lambda Function**, copy/paste the full ARN for the Lambda function you created in your second account and choose the checkmark\.

1. You'll see a popup that says **Add Permission to Lambda Function: You have selected a Lambda function from another account\. Please ensure that you have the appropriate Function Policy on this function\. You can do this by running the following AWS CLI command from account *123456789012*:**, followed by an `aws lambda add-permission` command string\.

1. Copy\-paste the `aws lambda add-permission` command string into an AWS CLI window that is configured for your second account\. This will grant your first account access to your second account's Lambda function\.

1. In the popup from the previous step, choose **OK**\.

1. To see the updated policy for your function in the Lambda console, 

   1. Choose your integration function\.

   1. In the **Designer** pane, choose the key icon\.

   In the **Function policy** pane, you should now see an `Allow` policy with a `Condition` clause in which the in the `AWS:SourceArn` is the ARN for your API's `GET` method\.