# Set up API Gateway private integrations<a name="set-up-private-integration"></a>

 The API Gateway private integration makes it simple to expose your HTTP/HTTPS resources within an Amazon VPC for access by clients outside of the VPC\. To extend access to your private VPC resources beyond the VPC boundaries, you can create an API with private integration\. You can control access to your API by using any of the [authorization methods](apigateway-control-access-to-api.md) that API Gateway supports\.

To create a private integration, you must first create a Network Load Balancer\. Your Network Load Balancer must have a [listener](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/load-balancer-listeners.html) that routes requests to resources in your VPC\. Then, you create a VPC link that you use to connect your API and your Network Load Balancer\. After you create a VPC link, you create private integrations to route traffic from your API to resources in your VPC through your VPC link and Network Load Balancer\.

**Note**  
The Network Load Balancer and API must be owned by the same AWS account\.

With the API Gateway private integration, you can enable access to HTTP/HTTPS resources within a VPC without detailed knowledge of private network configurations or technology\-specific appliances\.

**Topics**
+ [Set up a Network Load Balancer for API Gateway private integrations](set-up-nlb-for-vpclink-using-console.md)
+ [Grant permissions to create a VPC link](grant-permissions-to-create-vpclink.md)
+ [Set up an API Gateway API with private integrations using the API Gateway console](set-up-api-with-vpclink-console.md)
+ [Set up an API Gateway API with private integrations using the AWS CLI](set-up-api-with-vpclink-cli.md)
+ [Set up API with private integrations using OpenAPI](set-up-api-with-vpclink-using-swagger.md)
+ [API Gateway accounts used for private integrations](set-up-api-with-vpclink-accounts.md)