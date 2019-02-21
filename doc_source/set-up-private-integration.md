# Set up API Gateway Private Integrations<a name="set-up-private-integration"></a>

 The API Gateway private integration makes it simple to expose your HTTP/HTTPS resources behind an Amazon VPC for access by clients outside of the VPC\. To extend access to your private VPC resources beyond the VPC boundaries, you can create an API with private integration for open access or controlled access\. You can do this by using IAM permissions, a Lambda authorizer, or an Amazon Cognito user pool\. 

The private integration uses an API Gateway resource of `VpcLink` to encapsulate connections between API Gateway and targeted VPC resources\. As an owner of a VPC resource, you are responsible for creating a network load balancer in your VPC and adding a VPC resource as a target of a network load balancer's listener\. As an API developer, to set up an API with the private integration, you are responsible for creating a `VpcLink` targeting specified network load balancers and then treating the `VpcLink` as an effective integration endpoint\.

With the API Gateway private integration, you can enable access to HTTP/HTTPS resources within a VPC without detailed knowledge of private network configurations or technology\-specific appliances\.

**Topics**
+ [Set up a Network Load Balancer for API Gateway Private Integrations](set-up-nlb-for-vpclink-using-console.md)
+ [Grant Permissions to Create a VPC Link](grant-permissions-to-create-vpclink.md)
+ [Set up an API Gateway API with Private Integrations Using the API Gateway Console](set-up-api-with-vpclink-console.md)
+ [Set up an API Gateway API with Private Integrations Using the AWS CLI](set-up-api-with-vpclink-cli.md)
+ [Set up API with Private Integrations Using OpenAPI](set-up-api-with-vpclink-using-swagger.md)