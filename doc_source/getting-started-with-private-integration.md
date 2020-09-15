# Tutorial: Build a REST API with API Gateway private integration<a name="getting-started-with-private-integration"></a>

 You can create an API Gateway API with private integration to provide your customers access to HTTP/HTTPS resources within your Amazon Virtual Private Cloud \(Amazon VPC\)\. Such VPC resources are HTTP/HTTPS endpoints on an EC2 instance behind a Network Load Balancer in the VPC\. The Network Load Balancer encapsulates the VPC resource and routes incoming requests to the targeted resource\. 

When a client calls the API, API Gateway connects to the Network Load Balancer through the pre\-configured VPC link\. A VPC link is encapsulated by an API Gateway resource of [VpcLink](https://docs.aws.amazon.com/apigateway/api-reference/resource/vpc-link/)\. It is responsible for forwarding API method requests to the VPC resources and returns backend responses to the caller\. For an API developer, a `VpcLink` is functionally equivalent to an integration endpoint\. 

 To create an API with private integration, you must create a new `VpcLink`, or choose an existing one, that is connected to a Network Load Balancer that targets the desired VPC resources\. You must have [appropriate permissions](grant-permissions-to-create-vpclink.md) to create and manage a `VpcLink`\. You then set up an API [method](https://docs.aws.amazon.com/apigateway/api-reference/resource/method/) and integrate it with the `VpcLink` by setting either `HTTP` or `HTTP_PROXY` as the [integration type](https://docs.aws.amazon.com/apigateway/api-reference/resource/integration/#type), setting `VPC_LINK` as the integration [connection type](https://docs.aws.amazon.com/apigateway/api-reference/resource/integration/#connectionType), and setting the `VpcLink` identifier on the integration [https://docs.aws.amazon.com/apigateway/api-reference/resource/integration/#connectionId](https://docs.aws.amazon.com/apigateway/api-reference/resource/integration/#connectionId)\. 

**Note**  
The Network Load Balancer and API must be owned by the same AWS account\.

To quickly get started creating an API to access VPC resources, we walk through the essential steps for building an API with the private integration, using the API Gateway console\. Before creating the API, do the following: 

1.  Create a VPC resource, create or choose a Network Load Balancer under your account in the same region, and add the EC2 instance hosting the resource as a target of the Network Load Balancer\. For more information, see [Set up a Network Load Balancer for API Gateway private integrations](set-up-nlb-for-vpclink-using-console.md)\.

1.  Grant permissions to create the VPC links for private integrations\. For more information, see [Grant permissions to create a VPC link](grant-permissions-to-create-vpclink.md)\.

After creating your VPC resource and your Network Load Balancer with your VPC resource configured in its target groups, follow the instructions below to create an API and integrate it with the VPC resource via a `VpcLink` in a private integration\. 

**To create an API with private integration using the API Gateway console**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Create a VPC link, if you have not already done so:

   1. From the primary navigation pane, choose **VPC links** and then choose **Create**\. 

   1. Choose **VPC link for REST APIs**\. 

   1. Enter a name, and optionally, a description for your VPC link\.

   1.  Choose a Network Load Balancer from the **Target NLB** drop\-down list\.

      You must have the Network Load Balancer already created in the same Region as your API for the Network Load Balancer to be present in the list\.

   1.  Choose **Create** to start creating the VPC link\.

    The initial response returns a `VpcLink` resource representation with the VPC link ID and a `PENDING` status\. This is because the operation is asynchronous and takes about 2\-4 minutes to complete\. Upon successful completion, the status is `AVAILABLE`\. In the meantime, you can proceed to create the API\. 

1.  Choose **APIs** from the primary navigation pane and then choose **Create API** to create a new API of either an edge\-optimized or regional endpoint type\. 

1.  For the root resource \(/\), choose **Create Method** from the **Actions** drop\-down menu, and then choose `GET`\.

1. In the **/ GET \- Setup** pane, initialize the API method integration as follows: 

   1. Choose `VPC Link` for **Integration type**\.

   1.  Choose **Use Proxy Integration**\. 

   1.  From the **Method** drop\-down list, choose `GET` as the integration method\.

   1.  From the **VPC Link** drop\-down list, choose `[Use Stage Variables]` and type `${stageVariables.vpcLinkId}` in the text box below\.

      We will define the `vpcLinkId` stage variable after deploying the API to a stage and set its value to the ID of the `VpcLink` created in **Step 2**\.

   1.  Type a URL, for example, `http://myApi.example.com`, for **Endpoint URL**\. 

      Here, the host name \(for example, `myApi.example.com`\) is used to set the `Host` header of the integration request\. 
**Note**  
For the Network Load Balancer \(NLB\), be sure to use the NLB DNS name as described in [Getting Started with Network Load Balancers](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/network-load-balancer-getting-started.html)\. 

   1.  Leave the **Use Default Timeout** selection as\-is, unless you want to customize the integration timeouts\.

   1.  Choose **Save** to finish setting up the integration\.

      With the proxy integration, the API is ready for deployment\. Otherwise, you need to proceed to set up appropriate method responses and integration responses\.

   1.  From the **Actions** drop\-down menu, choose **Deploy API** and then choose a new or existing stage to deploy the API\. 

      Note the resulting **Invoke URL**\. You need it to invoke the API\. Before doing that, you must set up the `vpcLinkId` stage variable\.

   1.  In the **Stage Editor**, choose the **Stage Variables** tab and choose **Add Stage Variable**\. 

      1.  Under the **Name** column, type `vpcLinkId`\.

      1.  Under the **Value** column, type the ID of `VPC_LINK`, for example, `gix6s7`\.

      1.  Choose the check\-mark icon to save this stage variable\. 

         Using the stage variable, you can easily switch to different VPC links for the API by changing the stage variable value\.

      This completes creating the API\. You can test invoking the API as with other integrations\.
