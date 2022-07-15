# Choosing between REST APIs and HTTP APIs<a name="http-api-vs-rest"></a>

## <a name="http-api-vs-rest.differences"></a>

REST APIs and HTTP APIs are both RESTful API products\. REST APIs support more features than HTTP APIs, while HTTP APIs are designed with minimal features so that they can be offered at a lower price\. Choose REST APIs if you need features such as API keys, per\-client throttling, request validation, AWS WAF integration, or private API endpoints\. Choose HTTP APIs if you don't need the features included with REST APIs\.

The following sections summarize core features that are available in REST APIs and HTTP APIs\.

## Endpoint type<a name="http-api-vs-rest.differences.endpoint-type"></a>

The endpoint type refers to the endpoint that API Gateway creates for your API\. For more information, see [Choose an endpoint type to set up for an API Gateway API](api-gateway-api-endpoint-types.md)\. 


| Endpoint types | REST API | HTTP API | 
| --- | --- | --- | 
|  [Edge\-optimized](api-gateway-api-endpoint-types.md#api-gateway-api-endpoint-types-edge-optimized)  |  ✓  |    | 
|  [Regional](api-gateway-api-endpoint-types.md#api-gateway-api-endpoint-types-regional)  |  ✓  |  ✓  | 
|  [Private](api-gateway-api-endpoint-types.md#api-gateway-api-endpoint-types-private)  |  ✓  |    | 

## Security<a name="http-api-vs-rest.differences.security"></a>

API Gateway provides a number of ways to protect your API from certain threats, like malicious actors or spikes in traffic\. To learn more, see [Protecting your REST API](rest-api-protect.md) and [Protecting your HTTP API](http-api-protect.md)\.


| Security features | REST API | HTTP API | 
| --- | --- | --- | 
|  [Mutual TLS authentication](rest-api-mutual-tls.md)  |  [✓](rest-api-mutual-tls.md)  |  [✓](http-api-mutual-tls.md)  | 
|  [Certificates for backend authentication](getting-started-client-side-ssl-authentication.md)  |  ✓  |    | 
|  [AWS WAF](apigateway-control-access-aws-waf.md)  |  ✓  |    | 

## Authorization<a name="http-api-vs-rest.differences.authorization"></a>

API Gateway supports multiple mechanisms for controlling and managing access to your API\. For more information, see [Controlling and managing access to a REST API in API Gateway](apigateway-control-access-to-api.md) and [Controlling and managing access to an HTTP API in API Gateway](http-api-access-control.md)\.


| Authorization options | REST API | HTTP API | 
| --- | --- | --- | 
|  [IAM](permissions.md)  |  [✓](permissions.md)  |  [✓](http-api-access-control-iam.md)  | 
|  [Resource policies](apigateway-resource-policies.md)  |  ✓  |    | 
|  [Amazon Cognito](apigateway-integrate-with-cognito.md)  |  ✓  |  ✓ 1  | 
|  [Custom authorization with an AWS Lambda function](apigateway-use-lambda-authorizer.md)  |  [✓](apigateway-use-lambda-authorizer.md)  |  [✓](http-api-lambda-authorizer.md)  | 
|  [JSON Web Token \(JWT\)](http-api-jwt-authorizer.md) 2  |    |  ✓  | 

1 You can use Amazon Cognito with a [JWT authorizer](http-api-jwt-authorizer.md)\.

2 You can use a [Lambda authorizer](apigateway-use-lambda-authorizer.md) to validate JWTs for REST APIs\.

## API management<a name="http-api-vs-rest.differences.management"></a>

Choose REST APIs if you need API management capabilities such as API keys and per\-client rate limiting\. For more information, see [Distributing your REST API to clients](rest-api-distribute.md), [Setting up custom domain names for REST APIs](how-to-custom-domains.md), and [Setting up custom domain names for HTTP APIs](http-api-custom-domain-names.md)\.


| Features | REST API | HTTP API | 
| --- | --- | --- | 
|  [Custom domains](how-to-custom-domains.md)  |  [✓](how-to-custom-domains.md)  |  [✓](http-api-custom-domain-names.md)  | 
|  [API keys](api-gateway-api-usage-plans.md)  |  ✓  |    | 
|  [Per\-client rate limiting](api-gateway-request-throttling.md)  |  ✓  |    | 
|  [Per\-client usage throttling](api-gateway-api-usage-plans.md)  |  ✓  |    | 

## Development<a name="http-api-vs-rest.differences.development"></a>

As you're developing your API Gateway API, you decide on a number of characteristics of your API\. These characteristics depend on the use case of your API\. For more information see [Developing a REST API in API Gateway](rest-api-develop.md) and [Developing an HTTP API in API Gateway](http-api-develop.md)\.


| Features | REST API | HTTP API | 
| --- | --- | --- | 
|  [CORS configuration](how-to-cors.md)  |  [✓](how-to-cors.md)  |  [✓](http-api-cors.md)  | 
|  [Test invocations](how-to-test-method.md)  |  ✓  |    | 
|  [Caching](api-gateway-caching.md)  |  ✓  |    | 
|  [User\-controlled deployments](how-to-deploy-api.md)  |  [✓](how-to-deploy-api.md)  |  [✓](http-api-stages.md)  | 
|  [Automatic deployments](http-api-stages.md)  |    |  ✓  | 
|  [Custom gateway responses](api-gateway-gatewayResponse-definition.md)  |  ✓  |    | 
|  [Canary release deployments](canary-release.md)  |  ✓  |    | 
|  [Request validation](api-gateway-method-request-validation.md)  |  ✓  |    | 
|  [Request parameter transformation](rest-api-data-transformations.md)  |  [✓](rest-api-data-transformations.md)  |  [✓](http-api-parameter-mapping.md)  | 
|  [Request body transformation](rest-api-data-transformations.md)  |  ✓  |    | 

## Monitoring<a name="http-api-vs-rest.differences.monitoring"></a>

API Gateway supports several options to log API requests and monitor your APIs\. For more information, see [Monitoring REST APIs](rest-api-monitor.md) and [Monitoring your HTTP API](http-api-monitor.md)\.


| Feature | REST API | HTTP API | 
| --- | --- | --- | 
|  [Amazon CloudWatch metrics](monitoring-cloudwatch.md)  |  [✓](monitoring-cloudwatch.md)  |  [✓](http-api-metrics.md)  | 
|  [Access logs to CloudWatch Logs](set-up-logging.md)  |  [✓](set-up-logging.md)  |  [✓](http-api-logging.md)  | 
|  [Access logs to Amazon Kinesis Data Firehose](apigateway-logging-to-kinesis.md)  |  ✓  |    | 
|  [Execution logs](set-up-logging.md)  |  ✓  |    | 
|  [AWS X\-Ray tracing](apigateway-xray.md)  |  ✓  |    | 

## Integrations<a name="http-api-vs-rest.differences.integrations"></a>

Integrations connect your API Gateway API to backend resources\. For more information, see [Setting up REST API integrations](how-to-integration-settings.md) and [Configuring integrations for HTTP APIs](http-api-develop-integrations.md)\.


| Feature | REST API | HTTP API | 
| --- | --- | --- | 
|  [Public HTTP endpoints](setup-http-integrations.md)  |  [✓](setup-http-integrations.md)  |  [✓](http-api-develop-integrations-http.md)  | 
|  [AWS services](api-gateway-api-integration-types.md)  |  [✓](api-gateway-api-integration-types.md)  |  [✓](http-api-develop-integrations-aws-services.md)  | 
|  [AWS Lambda functions](set-up-lambda-integrations.md)  |  [✓](set-up-lambda-integrations.md)  |  [✓](http-api-develop-integrations-lambda.md)  | 
|  [Private integrations with Network Load Balancers](set-up-private-integration.md)  |  [✓](set-up-private-integration.md)  |  [✓](http-api-develop-integrations-private.md)  | 
|  [Private integrations with Application Load Balancers](http-api-develop-integrations-private.md)  |    |  ✓  | 
|  [Private integrations with AWS Cloud Map](http-api-develop-integrations-private.md)  |    |  ✓  | 
|  [Mock integrations](how-to-mock-integration.md)  |  ✓  |    | 