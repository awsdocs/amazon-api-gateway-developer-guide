# Choose an Endpoint Type to Set up for an API Gateway API<a name="api-gateway-api-endpoint-types"></a>

An *[API endpoint](api-gateway-basic-concept.md#apigateway-definition-api-endpoints)* refers to a host name of the API\. The API endpoint type can be *edge\-optimized*, *regional*, or *private*, depending on where the majority of your API traffic originates from\.

An *[edge\-optimized API endpoint](api-gateway-basic-concept.md#apigateway-definition-edge-optimized-api-endpoint)* is best for geographically distributed clients\. API requests are routed to the nearest CloudFront Point of Presence \(POP\)\.

A *[regional API endpoint](api-gateway-basic-concept.md#apigateway-definition-regional-api-endpoint)* is intended for clients in the same region\. When a client running on an EC2 instance calls an API in the same region, or when an API is intended to serve a small number of clients with high demands, a regional API reduces connection overhead\. For more information, see [Set up a Regional API in API Gateway](create-regional-api.md)\.

For an edge\-optimized API, any custom domain name that you use applies across all regions\. For a regional API, the custom domain name is specific to the region where the API is deployed\. If you deploy a regional API deployed in multiple regions, it can have the same custom domain name in all regions\. You can use custom domains together with Amazon Route 53 to perform tasks such as [latency\-based routing](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy.html#routing-policy-latency)\. For more information, see [Set up a Custom Domain Name for a Regional API in API Gateway](apigateway-regional-api-custom-domain-create.md) and [How to Create an Edge\-Optimized Custom Domain Name](how-to-edge-optimized-custom-domain-name.md)\.

**Note**  
Edge\-optimized APIs capitalize the names of [HTTP headers](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers) \(for example, `Cookie`\)\. Regional and private APIs pass all header names through as\-is\.

A *[private API endpoint](api-gateway-basic-concept.md#apigateway-definition-private-api-endpoint)* is an API endpoint that can only be accessed from your Amazon Virtual Private Cloud \(VPC\) using an interface VPC endpoint, which is an endpoint network interface \(ENI\) that you create in your VPC\. For more information, see [Create a Private API in Amazon API Gateway](apigateway-private-apis.md)\.