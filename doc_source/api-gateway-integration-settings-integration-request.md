# Set up an integration request in API Gateway<a name="api-gateway-integration-settings-integration-request"></a>

To set up an integration request, you perform the following required and optional tasks:

1.  Choose an integration type that determines how method request data is passed to the backend\.

1.  For non\-mock integrations, specify an HTTP method and the URI of the targeted integration endpoint, except for the `MOCK` integration\.

1.  For integrations with Lambda functions and other AWS service actions, set an IAM role with required permissions for API Gateway to call the backend on your behalf\.

1.  For non\-proxy integrations, set necessary parameter mappings to map predefined method request parameters to appropriate integration request parameters\.

1.  For non\-proxy integrations, set necessary body mappings to map the incoming method request body of a given content type according to the specified mapping template\.

1.  For non\-proxy integrations, specify the condition under which the incoming method request data is passed through to the backend as\-is\. 

1.  Optionally, specify how to handle type conversion for a binary payload\.

1.  Optionally, declare a cache namespace name and cache key parameters to enable API caching\. 

 Performing these tasks involves creating an [Integration](https://docs.aws.amazon.com/apigateway/api-reference/resource/integration/) resource of API Gateway and setting appropriate property values\. You can do so using the API Gateway console, AWS CLI commands, an AWS SDK, or the API Gateway REST API\. 

**Topics**
+ [Basic tasks of an API integration request](integration-request-basic-setup.md)
+ [Choose an API Gateway API integration type](api-gateway-api-integration-types.md)
+ [Set up a proxy integration with a proxy resource](api-gateway-set-up-simple-proxy.md)
+ [Set up an API integration request using the API Gateway console](how-to-method-settings-console.md)