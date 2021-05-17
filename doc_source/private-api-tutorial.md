# Tutorial: Building a private REST API<a name="private-api-tutorial"></a>

In this tutorial, you create a private REST API\. Clients can access the API only from within your Amazon VPC\. The API is isolated from the public internet, which is a common security requirement\.

This tutorial takes approximately 30 minutes to complete\. First, you use an AWS CloudFormation template to create an Amazon VPC, a VPC endpoint, an AWS Lambda function, and launch an Amazon EC2 instance that you'll use to test your API\. Next, you use the AWS Management Console to create a private API and attach a resource policy that allows access only from your VPC endpoint\. Lastly, you test your API\. 

![\[Architectural overview of the API that you create in this tutorial. Your private API is accessibly only from within your Amazon VPC.\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/private-api-tutorial-diagram.png)

To complete this tutorial, you need an AWS account and an AWS Identity and Access Management user with console access\. For more information, see [Prerequisites for getting started with API Gateway](setting-up.md)\.

In this tutorial, you use the AWS Management Console\. For an AWS CloudFormation template that creates this API and all related resources, see [template\.yaml](samples/private-api-full-template.zip)\.

**Topics**
+ [Step 1: Create dependencies](#private-api-tutorial-create-dependencies)
+ [Step 2: Create a private API](#private-api-tutorial-create-api)
+ [Step 3: Create a method and integration](#private-api-tutorial-create-method)
+ [Step 4: Attach a resource policy](#private-api-tutorial-attach-resource-policy)
+ [Step 5: Deploy your API](#private-api-tutorial-deploy-api)
+ [Step 6: Verify that your API isn't publicly accessible](#private-api-tutorial-test-private-api)
+ [Step 7: Connect to an instance in your VPC and invoke your API](#private-api-tutorial-connect-to-instance)
+ [Step 8: Clean up](#private-api-tutorial-cleanup)
+ [Next steps: Automate with AWS CloudFormation](#private-api-tutorial-next-steps)

## Step 1: Create dependencies<a name="private-api-tutorial-create-dependencies"></a>

Download and unzip [this AWS CloudFormation template](samples/private-api-starter-template.zip)\. You use the template to create all of the dependencies for your private API, including an Amazon VPC, a VPC endpoint, and a Lambda function that serves as the backend of your API\. You create the private API later\.

**To create a AWS CloudFormation stack**

1. Open the AWS CloudFormation console at [https://console\.aws\.amazon\.com/cloudformation](https://console.aws.amazon.com/cloudformation/)\.

1. Choose **Create stack** and then choose **With new resources \(standard\)**\.

1. For **Specify template**, choose **Upload a template file**\.

1. Select the template that you downloaded\.

1. Choose **Next**\. 

1. For **Stack name**, enter **private\-api\-tutorial** and then choose **Next**\.

1. For **Configure stack options**, choose **Next**\.

1. For **Capabilities**, acknowledge that AWS CloudFormation can create IAM resources in your account\.

1. Choose **Create stack**\.

AWS CloudFormation provisions the dependencies for your API, which can take a few minutes\. When the status of your AWS CloudFormation stack is **CREATE\_COMPLETE**, choose **Outputs**\. Note your VPC endpoint ID\. You need it for later steps in this tutorial\. 

## Step 2: Create a private API<a name="private-api-tutorial-create-api"></a>

You create a private API to allow only clients within your VPC to access it\.

**To create a private API**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Choose **Create API**, and then for **REST API**, choose **Build**\.

1. For **API name**, enter **private\-api\-tutorial**\.

1. For **Endpoint Type**, choose **Private**\.

1. For **VPC Endpoint IDs**, enter the VPC endpoint ID from the **Outputs** of your AWS CloudFormation stack\.

1. Choose **Create**\.

## Step 3: Create a method and integration<a name="private-api-tutorial-create-method"></a>

You create a `GET` method and Lambda integration to handle `GET` requests to your API\. When a client invokes your API, API Gateway sends the request to the Lambda function that you created in Step 1, and then returns a response to the client\.

**To create a method and integration**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Choose your API\.

1. Choose **Actions** and then choose **Create method**\.

1. Choose **GET** and then choose the check mark to confirm your choice\.

1. For **Integration Type**, select **Lambda function**

1. Select **Use Lambda proxy integration**\. With a Lambda proxy integration, API Gateway sends an event to Lambda with a defined structure, and transforms the response from your Lambda function to an HTTP response\.

1. For **Lambda function**, choose the function that you created with the AWS CloudFormation template in Step 1\. The function's name begins with **private\-api\-tutorial**\.

1. Choose **Save** and then choose **OK** to confirm that you grant API Gateway permission to invoke your Lambda function\.

## Step 4: Attach a resource policy<a name="private-api-tutorial-attach-resource-policy"></a>

You attach a [resource policy](apigateway-resource-policies.md) to your API that allows clients to invoke your API only through your VPC endpoint\. To further restrict access to your API, you can also configure a [ VPC endpoint policy](apigateway-vpc-endpoint-policies.md) for your VPC endpoint, but that's not necessary for this tutorial\.

**To attach a resource policy**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Choose your API\.

1. Choose **Resource policy**\.

1. Enter the following policy\. Replace *vpceID* with your VPC endpoint ID from the **Outputs** of your AWS CloudFormation stack\.

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Deny",
               "Principal": "*",
               "Action": "execute-api:Invoke",
               "Resource": "execute-api:/*",
               "Condition": {
                   "StringNotEquals": {
                       "aws:sourceVpce": "vpceID"
                   }
               }
           },
           {
               "Effect": "Allow",
               "Principal": "*",
               "Action": "execute-api:Invoke",
               "Resource": "execute-api:/*"
           }
       ]
   }
   ```

1. Choose **Save**\.

## Step 5: Deploy your API<a name="private-api-tutorial-deploy-api"></a>

Next, you deploy your API to make it available to clients in your Amazon VPC\.

**To deploy an API**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Choose your API\.

1. Choose **Actions** and then choose **Deploy API**\.

1. For **Deployment stage**, choose **New Stage**\.

1. For **Stage name**, enter **test**\.

1. Choose **Deploy**\.

Now you're ready to test your API\.

## Step 6: Verify that your API isn't publicly accessible<a name="private-api-tutorial-test-private-api"></a>

Use `curl` to verify that you can't invoke your API from outside of your Amazon VPC\.

**To test your API**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Choose your API\.

1. Choose **Stages** and then choose the **test** stage\.

1. Copy your API's URL\. The URL looks like `https://abcdef123.execute-api.us-west-2.amazonaws.com/test`\. The VPC endpoint that you created in Step 1 has private DNS enabled, so you can use the provided URL to invoke your API\.

1. Use curl to attempt to invoke your API from outside of your VPC\.

   ```
   curl https://abcdef123.execute-api.us-west-2.amazonaws.com/test
   ```

   Curl indicates that your API's endpoint can't be resolved\. If you get a different response, go back to Step 2, and make sure that you choose **Private** for your API's endpoint type\.

   ```
   curl: (6) Could not resolve host: abcdef123.execute-api.us-west-2.amazonaws.com/test
   ```

Next, you connect to an Amazon EC2 instance in your VPC to invoke your API\.

## Step 7: Connect to an instance in your VPC and invoke your API<a name="private-api-tutorial-connect-to-instance"></a>

Next, you test your API from within your Amazon VPC\. To access your private API, you connect to an Amazon EC2 instance in your VPC and then use curl to invoke your API\. You use Systems Manager Session Manger to connect to your instance in the browser\.

**To test your API**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. Choose **Instances**\.

1. Choose the instance named **private\-api\-tutorial** that you created with the AWS CloudFormation template in Step 1\.

1. Choose **Connect** and then choose **Session Manager**\.

1. Choose **Connect** to launch a browser\-based session to your instance\.

1. In your Session Manager session, use curl to invoke your API\. You can invoke your API because you're using an instance in your Amazon VPC\.

   ```
   curl https://abcdef123.execute-api.us-west-2.amazonaws.com/test
   ```

   Verify that you get the response `Hello from Lambda!`\.

![\[You use Session Manager to invoke your API from within your Amazon VPC.\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/private-api-tutorial-invoke.png)

You successfully created an API that's accessible only from within your Amazon VPC and then verified that it works\.

## Step 8: Clean up<a name="private-api-tutorial-cleanup"></a>

To prevent unnecessary costs, delete the resources that you created as part of this tutorial\. The following steps delete your REST API and your AWS CloudFormation stack\.

**To delete a REST API**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. On the **APIs** page, select an API\. Choose **Actions**, choose **Delete**, and then confirm your choice\.

**To delete an AWS CloudFormation stack**

1. Open the AWS CloudFormation console at [https://console\.aws\.amazon\.com/cloudformation](https://console.aws.amazon.com/cloudformation/)\.

1. Select your AWS CloudFormation stack\.

1. Choose **Delete** and then confirm your choice\.

## Next steps: Automate with AWS CloudFormation<a name="private-api-tutorial-next-steps"></a>

You can automate the creation and cleanup of all AWS resources involved in this tutorial\. For a full example AWS CloudFormation template, see [template\.yaml](samples/private-api-full-template.zip)\.