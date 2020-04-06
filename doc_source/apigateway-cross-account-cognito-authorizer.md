# Configure cross\-account Amazon Cognito authorizer for a REST API using the API Gateway console<a name="apigateway-cross-account-cognito-authorizer"></a>

You can now also use a Amazon Cognito user pool from a different AWS account as your API authorizer\. Each account can be in any region where Amazon API Gateway is available\. The Amazon Cognito user pool can use bearer token authentication strategies such as OAuth or SAML\. This makes it easy to centrally manage and share a central Amazon Cognito user pool authorizer across multiple API Gateway APIs\.

In this section, we show how to configure a cross\-account Amazon Cognito user pool using the Amazon API Gateway console\.

These instructions assume that you already have an API Gateway API in one AWS account and a Amazon Cognito user pool in another account\.

## Configure cross\-account Amazon Cognito authorizer using the API Gateway console<a name="apigateway-configure-cross-account-cognito-authorizer"></a>

Log in to the Amazon API Gateway console in your first account \(the one that has your API in it\) and do the following:

1. Locate your API and choose **Authorizers**\.

1. Choose **Create New Authorizer**\. 

1. For **Create Authorizer**, type an authorizer name in the **Name** input field\. 

1. For **Type**, choose the **Cognito** option\. 

1. For **Cognito User Pool**, copy\-paste the full ARN for the user pool that you have in your second account\.
**Note**  
In the Amazon Cognito console, you can find the ARN for your user pool in the **Pool ARN** field of the **General Settings** pane\.

1. Type the name of a header in **Token Source**\. The API client must include a header of this name to send the authorization token to the Amazon Cognito authorizer\. 

1. Optionally, provide a RegEx statement in **Token Validation** input field\. API Gateway performs initial validation of the input token against this expression and invokes the authorizer upon successful validation\. This helps reduce chances of being charged for invalid tokens\.

1. Choose **Create** to create the new Amazon Cognito authorizer for your API\.