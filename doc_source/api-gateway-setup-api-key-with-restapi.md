# Set up API keys using the API Gateway REST API<a name="api-gateway-setup-api-key-with-restapi"></a>

To set up API keys, do the following:
+ Configure API methods to require an API key\.
+ Create or import an API key for the API in a region\.

Before setting up API keys, you must have created an API and deployed it to a stage\. 

For the REST API calls to create and deploy an API, see [https://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-create/](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-create/) and [https://docs.aws.amazon.com/apigateway/api-reference/link-relation/deployment-create/](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/deployment-create/), respectively\.

**Topics**
+ [Require an API key on a method](#api-gateway-usage-plan-require-apikey-on-method-with-restapi)
+ [Create or import API keys](#api-gateway-usage-plan-create-apikey-with-restapi)

## Require an API key on a method<a name="api-gateway-usage-plan-require-apikey-on-method-with-restapi"></a>

To require an API key on a method, do one of the following:
+ Call [https://docs.aws.amazon.com/apigateway/api-reference/link-relation/method-put/](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/method-put/) to create a method\. Set `apiKeyRequired` to `true` in the request payload\.
+ Call [https://docs.aws.amazon.com/apigateway/api-reference/link-relation/method-update/](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/method-update/) to set `apiKeyRequired` to `true`\.

## Create or import API keys<a name="api-gateway-usage-plan-create-apikey-with-restapi"></a>

To create or import an API key, do one of the following:
+ Call [https://docs.aws.amazon.com/apigateway/api-reference/link-relation/apikey-create/](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/apikey-create/) to create an API key\.
+ Call [https://docs.aws.amazon.com/apigateway/api-reference/link-relation/apikey-import/](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/apikey-import/) to import an API key from a file\. For the file format, see [API Gateway API key file format](api-key-file-format.md)\.

With the API key created, you can now proceed to [Create, configure, and test usage plans using the API Gateway CLI and REST API](api-gateway-create-usage-plans-with-rest-api.md)\.