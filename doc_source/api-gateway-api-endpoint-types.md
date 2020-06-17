# Choose an endpoint type to set up for an API Gateway API<a name="api-gateway-api-endpoint-types"></a>

An *[API endpoint](api-gateway-basic-concept.md#apigateway-definition-api-endpoints)* type refers to the hostname of the API\. The API endpoint type can be *edge\-optimized*, *regional*, or *private*, depending on where the majority of your API traffic originates from\.

## Edge\-optimized API endpoints<a name="api-gateway-api-endpoint-types-edge-optimized"></a>

An *[edge\-optimized API endpoint](api-gateway-basic-concept.md#apigateway-definition-edge-optimized-api-endpoint)* is best for geographically distributed clients\. API requests are routed to the nearest CloudFront Point of Presence \(POP\)\. This is the default endpoint type for API Gateway REST APIs\.

Edge\-optimized APIs capitalize the names of [HTTP headers](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers) \(for example, `Cookie`\)\.

CloudFront sorts HTTP cookies in natural order by cookie name before forwarding the request to your origin\. For more information about the way CloudFront processes cookies, see [Caching Content Based on Cookies](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Cookies.html)\.

Any custom domain name that you use for an edge\-optimized API applies across all regions\.

## Regional API endpoints<a name="api-gateway-api-endpoint-types-regional"></a>

A *[regional API endpoint](api-gateway-basic-concept.md#apigateway-definition-regional-api-endpoint)* is intended for clients in the same region\. When a client running on an EC2 instance calls an API in the same region, or when an API is intended to serve a small number of clients with high demands, a regional API reduces connection overhead\.

For a regional API, any custom domain name that you use is specific to the region where the API is deployed\. If you deploy a regional API in multiple regions, it can have the same custom domain name in all regions\. You can use custom domains together with Amazon Route 53 to perform tasks such as [latency\-based routing](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy.html#routing-policy-latency)\. For more information, see [Setting up a regional custom domain name in API Gateway](apigateway-regional-api-custom-domain-create.md) and [Creating an edge\-optimized custom domain name](how-to-edge-optimized-custom-domain-name.md)\.

Regional API endpoints pass all header names through as\-is\.

## Private API endpoints<a name="api-gateway-api-endpoint-types-private"></a>

A *[private API endpoint](api-gateway-basic-concept.md#apigateway-definition-private-api-endpoint)* is an API endpoint that can only be accessed from your Amazon Virtual Private Cloud \(VPC\) using an interface VPC endpoint, which is an endpoint network interface \(ENI\) that you create in your VPC\. For more information, see [Creating a private API in Amazon API Gateway](apigateway-private-apis.md)\.

Private API endpoints pass all header names through as\-is\.