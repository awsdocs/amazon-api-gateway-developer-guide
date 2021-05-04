# Tutorial: Building an HTTP API with a private integration to an Amazon ECS service<a name="http-api-private-integration"></a>

In this tutorial, you create a serverless API that connects to an Amazon ECS service that runs in an Amazon VPC\. Clients outside of your Amazon VPC can use the API to access your Amazon ECS service\. 

This tutorial takes approximately an hour to complete\. First, you use an AWS CloudFormation template to create a Amazon VPC and Amazon ECS service\. Then you use the API Gateway console to create a VPC link\. The VPC link allows API Gateway to access the Amazon ECS service that runs in your Amazon VPC\. Next, you create an HTTP API that uses the VPC link to connect to your Amazon ECS service\. Lastly, you test your API\.

When you invoke your HTTP API, API Gateway routes the request to your Amazon ECS service through your VPC link, and then returns the response from the service\.

![\[Architectural overview of the API that you create in this tutorial. Clients use an API Gateway HTTP API to access your Amazon ECS service through a VPC link.\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/private-integration.png)

To complete this tutorial, you need an AWS account and an AWS Identity and Access Management user with console access\. For more information, see [Prerequisites for getting started with API Gateway](setting-up.md)\.

In this tutorial, you use the AWS Management Console\. For an AWS CloudFormation template that creates this API and all related resources, see [template\.yaml](samples/private-integration-full-template.zip)\.

**Topics**
+ [Step 1: Create an Amazon ECS service](#http-api-private-integration-create-ecs-service)
+ [Step 2: Create a VPC link](#http-api-private-integration-vpc-link)
+ [Step 3: Create an HTTP API](#http-api-private-integration-create-api)
+ [Step 4: Create a route](#http-api-private-integration-create-routes)
+ [Step 5: Create an integration](#http-api-private-integration-create-integration)
+ [Step 6: Test your API](#http-api-private-integration-invoke-api)
+ [Step 7: Clean up](#http-api-private-integration-cleanup)
+ [Next steps: Automate with AWS CloudFormation](#http-api-private-integration-next-steps)

## Step 1: Create an Amazon ECS service<a name="http-api-private-integration-create-ecs-service"></a>

Amazon ECS is a container management service that makes it easy to run, stop, and manage Docker containers on a cluster\. In this tutorial, you run your cluster on a serverless infrastructure that's managed by Amazon ECS\.

Download and unzip [this AWS CloudFormation template](samples/private-integration-cfn.zip), which creates all of the dependencies for the service, including an Amazon VPC\. You use the template to create an Amazon ECS service that uses an Application Load Balancer\.

**To create a AWS CloudFormation stack**

1. Open the AWS CloudFormation console at [https://console\.aws\.amazon\.com/cloudformation](https://console.aws.amazon.com/cloudformation/)\.

1. Choose **Create stack** and then choose **With new resources \(standard\)**\.

1. For **Specify template**, choose **Upload a template file**\.

1. Select the template that you downloaded\.

1. Choose **Next**\. 

1. For **Stack name**, enter **http\-api\-private\-integrations\-tutorial** and then choose **Next**\.

1. For **Configure stack options**, choose **Next**\.

1. For **Capabilities**, acknowledge that AWS CloudFormation can create IAM resources in your account\.

1. Choose **Create stack**\.

AWS CloudFormation provisions the ECS service, which can take a few minutes\. When the status of your AWS CloudFormation stack is **CREATE\_COMPLETE**, you're ready to move on to the next step\.

## Step 2: Create a VPC link<a name="http-api-private-integration-vpc-link"></a>

A VPC link allows API Gateway to access private resources in an Amazon VPC\. You use a VPC link to allow clients to access your Amazon ECS service through your HTTP API\.

**To create a VPC link**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Choose **VPC links** and then choose **Create**\.

1. For **Choose a VPC link version**, select **VPC link for HTTP APIs**\.

1. For **Name**, enter **private\-integrations\-tutorial**\.

1. For **VPC**, choose the VPC that you created in step 1\. The name should start with **PrivateIntegrationsStack**\.

1. For **Subnets**, select the two private subnets in your VPC\. Their names end with `PrivateSubnet`\.

1. Choose **Create**\.

After you create your VPC link, API Gateway provisions Elastic Network Interfaces to access your VPC\. The process can take a few minutes\. In the meantime, you can create your API\.

## Step 3: Create an HTTP API<a name="http-api-private-integration-create-api"></a>

The HTTP API provides an HTTP endpoint for your Amazon ECS service\. In this step, you create an empty API\. In Steps 4 and 5, you configure a route and an integration to connect your API and your Amazon ECS service\.



**To create an HTTP API**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Choose **Create API**, and then for **HTTP API**, choose **Build**\.

1. For **API name**, enter **http\-private\-integrations\-tutorial**\.

1. Choose **Next**\.

1. For **Configure routes**, choose **Next** to skip route creation\. You create routes later\.

1. Review the stage that API Gateway creates for you\. API Gateway creates a `$default` stage with automatic deployments enabled, which is the best choice for this tutorial\. Choose **Next**\.

1. Choose **Create**\.

## Step 4: Create a route<a name="http-api-private-integration-create-routes"></a>

Routes are a way to send incoming API requests to backend resources\. Routes consist of two parts: an HTTP method and a resource path, for example, `GET /items`\. For this example API, we create one route\.

**To create a route**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Choose your API\.

1. Choose **Routes**\.

1. Choose **Create**\.

1. For **Method**, choose **ANY**\.

1. For the path, enter **/\{proxy\+\}**\. The `{proxy+}` at the end of the path is a greedy path variable\. API Gateway sends all requests to your API to this route\.

1. Choose **Create**\.

## Step 5: Create an integration<a name="http-api-private-integration-create-integration"></a>

You create an integration to connect a route to backend resources\.

**To create an integration**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Choose your API\.

1. Choose **Integrations**\.

1. Choose **Manage integrations** and then choose **Create**\.

1. For **Attach this integration to a route**, select the **ANY /\{proxy\+\}** route that you created earlier\.

1. For **Integration type**, choose **Private resource**\.

1. For **Integration details**, choose **Select manually**\.

1. For **Target service**, choose **ALB/NLB**\.

1. For **Load balancer**, choose the load balancer that you created with the AWS CloudFormation template in Step 1\. It's name should start with **http\-Priva**\.

1. For **Listener**, choose **HTTP 80**\.

1. For **VPC link**, choose the VPC link that you created in Step 2\. It's name should be `private-integrations-tutorial`\.

1. Choose **Create**\.

To verify that your route and integration are set up correctly, select **Attach integrations to routes**\. The console shows that you have an `ANY /{proxy+}` route with an integration to a VPC Load Balancer\.

![\[The console shows that you have a /{proxy+} route with an integration to Load Balancer in a VPC.\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/private-integration-tutorial-route.png)

Now you're ready to test your API\.

## Step 6: Test your API<a name="http-api-private-integration-invoke-api"></a>

Next, you test your API to make sure that it's working\. For simplicity, use a web browser to invoke your API\.

**To test your API**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Choose your API\.

1. Note your API's invoke URL\.  
![\[After you create your API, the console shows your API's invoke URL.\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/getting-started-invoke-url.png)

1. In a web browser, go to your API's invoke URL\.

   The full URL should look like `https://abcdef123.execute-api.us-east-2.amazonaws.com`\.

   Your browser sends a `GET` request to the API\.

1. Verify that your API's response is a welcome message that tells you that your app is running on Amazon ECS\.

   If you see the welcome message, you successfully created an Amazon ECS service that runs in an Amazon VPC, and you used an API Gateway HTTP API with a VPC link to access the Amazon ECS service\.

## Step 7: Clean up<a name="http-api-private-integration-cleanup"></a>

To prevent unnecessary costs, delete the resources that you created as part of this tutorial\. The following steps delete your VPC link, AWS CloudFormation stack, and HTTP API\.

**To delete an HTTP API**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. On the **APIs** page, select an API\. Choose **Actions**, choose **Delete**, and then confirm your choice\.

**To delete a VPC link**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Choose **VPC link**\.

1. Select your VPC link, choose **Delete**, and then confirm your choice\.

**To delete an AWS CloudFormation stack**

1. Open the AWS CloudFormation console at [https://console\.aws\.amazon\.com/cloudformation](https://console.aws.amazon.com/cloudformation/)\.

1. Select your AWS CloudFormation stack\.

1. Choose **Delete** and then confirm your choice\.

## Next steps: Automate with AWS CloudFormation<a name="http-api-private-integration-next-steps"></a>

You can automate the creation and cleanup of all AWS resources involved in this tutorial\. For a full example AWS CloudFormation template, see [template\.yaml](samples/private-integration-full-template.zip)\.