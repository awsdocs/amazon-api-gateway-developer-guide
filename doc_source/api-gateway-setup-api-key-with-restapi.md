# Set Up API Keys Using the API Gateway REST API<a name="api-gateway-setup-api-key-with-restapi"></a>

To set up API keys, do the following:
+ Configure API methods to require an API key\.
+ Create or import an API key for the API in a region\.

Before setting up API keys, you must have created an API and deployed it to a stage\. 

For the REST API calls to create and deploy an API, see [restapi:create](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-create/) and [deployment:create](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/deployment-create/), respectively\.

**Topics**
+ [Require an API Key on a Method](#api-gateway-usage-plan-require-apikey-on-method-with-restapi)
+ [Create or Import API Keys](#api-gateway-usage-plan-create-apikey-with-restapi)

## Require an API Key on a Method<a name="api-gateway-usage-plan-require-apikey-on-method-with-restapi"></a>

To require an API key on a method, do one of the following:
+ Call [method:put](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/method-put/) to create a method\. Set `apiKeyRequired` to `true` in the request payload\.
+ Call [method:update](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/method-update/) to set `apiKeyRequired` to `true`\.

## Create or Import API Keys<a name="api-gateway-usage-plan-create-apikey-with-restapi"></a>

To create or import an API key, do one of the following:
+ Call [apikey:create](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/apikey-create/) to create an API key\.
+ Call [apikey:import](http://docs.aws.amazon.com/apigateway/api-reference/link-relation/apikey-import/) to import an API key from a file\. For the file format, see [API Gateway API Key File Format](api-key-file-format.md)\.

With the API key created, you can now proceed to [Create, Configure, and Test Usage Plans Using the API Gateway REST API](api-gateway-create-usage-plans-with-rest-api.md)\.