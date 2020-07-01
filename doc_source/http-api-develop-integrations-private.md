# Working with private integrations for HTTP APIs<a name="http-api-develop-integrations-private"></a>

Private integrations enable you to create API integrations with private resources in a VPC, such as Application Load Balancers or Amazon ECS container\-based applications\. 

You can expose your resources in a VPC for access by clients outside of the VPC by using private integrations\. You can control access to your API by using [JWT authorizers](http-api-jwt-authorizer.md)\.

To create a private integration, you must first create a VPC link\. To learn more about VPC links, see [Working with VPC links for HTTP APIs](http-api-vpc-links.md)\.

After you’ve created a VPC link, you can set up private integrations that connect to an Application Load Balancer, Network Load Balancer, or resources registered with an AWS Cloud Map service\.

To create a private integration, all resources must be owned by the same AWS account \(including the load balancer or AWS Cloud Map service, VPC link and HTTP API\)\.

By default, private integration traffic uses the HTTP protocol\. You can specify a [https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/apis-apiid-integrations-integrationid.html](https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/apis-apiid-integrations-integrationid.html) if you require private integration traffic to use HTTPS\.

**Note**  
For private integrations, API Gateway includes the [stage](http-api-stages.md) portion of the API endpoint in the request to your backend resources\. For example, a request to the `test` stage of an API includes `test/route-path` in the request to your private integration\.

## Create a private integration using an Application Load Balancer or Network Load Balancer<a name="http-api-develop-integrations-private-ELB"></a>

Before you create a private integration, you must create a VPC link\. To learn more about VPC links, see [Working with VPC links for HTTP APIs](http-api-vpc-links.md)\.

To create a private integration with an Application Load Balancer or Network Load Balancer, create an HTTP proxy integration, specify the VPC link to use, and provide the listener ARN of the load balancer\.

Use the following command to create a private integration that connects to a load balancer by using a VPC link\.

```
aws apigatewayv2 create-integration --api-id api-id --integration-type HTTP_PROXY \
    --integration-method GET --connection-type VPC_LINK \
    --connection-id VPC-link-ID \
    --integration-uri arn:aws:elasticloadbalancing:us-east-2:123456789012:listener/app/my-load-balancer/50dc6c495c0c9188/0467ef3c8400ae65
    --payload-format-version 1.0
```

## Create a private integration using AWS Cloud Map service discovery<a name="http-api-develop-integrations-private-Cloud-Map"></a>

Before you create a private integration, you must create a VPC link\. To learn more about VPC links, see [Working with VPC links for HTTP APIs](http-api-vpc-links.md)\.

For integrations with AWS Cloud Map, API Gateway uses `DiscoverInstances` to identify resources\. You can use query parameters to target specific resources\. The registered resources' attributes must include IP addresses and ports\. API Gateway distributes requests across healthy resources that are returned from `DiscoverInstances`\. To learn more, see [DiscoverInstances](https://docs.aws.amazon.com/cloud-map/latest/api/API_DiscoverInstances.html) in the AWS Cloud Map API Reference\.

**Note**  
If you use Amazon ECS to populate entries in AWS Cloud Map, you must configure your Amazon ECS task to use SRV records\. "A" records aren't supported\. To learn more, see [Configuring Your Service to Use Service Discovery](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/service-configure-servicediscovery.html) in the Amazon Elastic Container Service Developer Guide\.

To create a private integration with AWS Cloud Map, create an HTTP proxy integration, specify the VPC link to use, and provide the ARN of the AWS Cloud Map service\.

Use the following command to create a private integration that uses AWS Cloud Map service discovery to identify resources\.

```
aws apigatewayv2 create-integration --api-id api-id --integration-type HTTP_PROXY  \
    --integration-method GET --connection-type VPC_LINK \
    --connection-id VPC-link-ID \
    --integration-uri arn:aws:servicediscovery:us-east-2:123456789012:service/srv-id?stage=prod&deployment=green_deployment
    --payload-format-version 1.0
```