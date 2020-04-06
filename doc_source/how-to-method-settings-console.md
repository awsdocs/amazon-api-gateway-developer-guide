# Set up an API integration request using the API Gateway console<a name="how-to-method-settings-console"></a>

 An API method setup defines the method and describes its behaviors\. To set up a method, you must specify a resource, including the root \("/"\), on which the method is exposed, an HTTP method \(`GET`, `POST`, etc\.\), and how it will be integrated with the targeted backend\. The method request and response specify the contract with the calling app, stipulating which parameters the API can receive and what the response looks like\. 

 The following procedure describes how to use the API Gateway console to specify method settings\. 

1. In the **Resources** pane, choose the method\.

1. In the **Method Execution** pane, choose **Integration Request**\. For **Integration type**, choose one of the following:
   + Choose **Lambda Function** if your API will be integrated with a Lambda function\. At the API level, this is an `AWS` integration type\.
   + Choose **HTTP** if your API will be integrated with an HTTP endpoint\. At the API level, this is the `HTTP` integration type\.
   + Choose **AWS Service** if your API will be integrated directly with an AWS service\. At the API level, this is the `AWS` integration type\. The **Lambda Function** option above is a special case of the `AWS` integration for invoking a Lambda function and is available only in the API Gateway console\. To set up an API Gateway API to create a new Lambda function in AWS Lambda, to set a resource permission on the Lambda function, or to perform any other Lambda service actions, you must choose the **AWS Service** option here\.
   +  Choose **Mock** if you want API Gateway to act as your backend to return static responses\. At the API level, this is the `MOCK` integration type\. Typically, you can use the `MOCK` integration when your API is not yet final, but you want to generate API responses to unblock dependent teams for testing\. For the `OPTION` method, API Gateway sets the `MOCK` integration as default to return CORS\-enabling headers for the applied API resource\. If you choose this option, skip the rest of the instructions in this topic and see [Set up mock integrations in API Gateway](how-to-mock-integration.md)\.

1. If you chose **Lambda Function**, do the following:

   1. For **Lambda Region**, choose the region identifier that corresponds to the region where you created the Lambda function\. For example, if you created the Lambda function in the US East \(N\. Virginia\) Region, choose **us\-east\-1**\. For a list of region names and identifiers, see [AWS Lambda](https://docs.aws.amazon.com/general/latest/gr/rande.html#lambda_region) in the *Amazon Web Services General Reference*\.

   1. For **Lambda Function**, type the name of the Lambda function, and then choose the function's corresponding ARN\.

   1. Choose **Save**\.

1. If you chose **HTTP**, do the following:

   1. For **HTTP method**, choose the HTTP method type that most closely matches the method in the HTTP backend\.

   1. For **Endpoint URL**, type the URL of the HTTP backend you want this method to use\.

   1. Choose **Save**\.

1. If you chose **Mock**, do the following:

   1. Choose **Save**\.

1. If you chose **AWS Service**, do the following:

   1. For **AWS Region**, choose the AWS Region you want this method to use to call the action\.

   1. For **AWS Service**, choose the AWS service you want this method to call\.

   1.  For **AWS Subdomain**, type the subdomain used by the AWS service\. Typically, you would leave this blank\. Some AWS services can support subdomains as part of the hosts\. Consult the service documentation for the availability and, if available, details\. 

   1. For **HTTP method**, choose the HTTP method type that corresponds to the action\. For HTTP method type, see the API reference documentation for the AWS service you chose for **AWS Service**\.

   1. For **Action**, type the action you want to use\. For a list of available actions, see the API reference documentation for the AWS service you chose for **AWS Service**\.

   1. For **Execution Role**, type the ARN of the IAM role that the method will use to call the action\.

      To create the IAM role, you can adapt the instructions in "To create the Lambda invocation role and its policies" and "To create the Lambda execution role and its policy" in the [Create Lambda Functions](getting-started-lambda-non-proxy-integration.md#getting-started-new-lambda) section\. Specify an access policy of the following format, with the desired number of action and resource statements:

      ```
      {
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Action": [
              "action-statement"
            ],
            "Resource": [
              "resource-statement"
            ]
          },
          ...
        ]
      }
      ```

      For the action and resource statement syntax, see the documentation for the AWS service you chose for **AWS Service**\.

      For the IAM role's trust relationship, specify the following, which enables API Gateway to take action on behalf of your AWS account:

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

   1. If the action you typed for **Action** provides a custom resource path that you want this method to use, for **Path Override**, type this custom resource path\. For the custom resource path, see the API reference documentation for the AWS service you chose for **AWS Service**\.

   1. Choose **Save**\.