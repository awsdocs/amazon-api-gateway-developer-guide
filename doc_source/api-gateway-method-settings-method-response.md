# Set up method responses in API Gateway<a name="api-gateway-method-settings-method-response"></a>

An API method response encapsulates the output of an API method request that the client will receive\. The output data includes an HTTP status code, some headers, and possibly a body\. 

With non\-proxy integrations, the specified response parameters and body can be mapped from the associated integration response data or can be assigned certain static values according to mappings\. These mappings are specified in the integration response\. The mapping can be an identical transformation that passes the integration response through as\-is\.

With a proxy integration, API Gateway passes the backend response through to the method response automatically\. There is no need for you to set up the API method response\. However, with the Lambda proxy integration, the Lambda function must return a result of [this output format](set-up-lambda-proxy-integrations.md#api-gateway-simple-proxy-for-lambda-output-format) for API Gateway to successfully map the integration response to a method response\. 

Programmatically, the method response setup amounts to creating a [MethodResponse](https://docs.aws.amazon.com/apigateway/api-reference/resource/method-response/) resource of API Gateway and setting the properties of [statusCode](https://docs.aws.amazon.com/apigateway/api-reference/resource/method-response/#statusCode), [responseParameters](https://docs.aws.amazon.com/apigateway/api-reference/resource/method-response/#responseParameters), and [responseModels](https://docs.aws.amazon.com/apigateway/api-reference/resource/method-response/#responseModels)\. 

When setting status codes for an API method, you should choose one as the default to handle any integration response of an unanticipated status code\. It is reasonable to set `500` as the default because this amounts to casting otherwise unmapped responses as a server\-side error\. For instructional reasons, the API Gateway console sets the `200` response as the default\. But you can reset it to the `500` response\. 

To set up a method response, you must have created the method request\. 

## Set up method response status code<a name="setup-method-response-status-code"></a>

The status code of a method response defines a type of response\. For example, responses of 200, 400, and 500 indicate successful, client\-side error and server\-side error responses, respectively\. 

To set up a method response status code, set the [https://docs.aws.amazon.com/apigateway/api-reference/resource/method-response/#statusCode](https://docs.aws.amazon.com/apigateway/api-reference/resource/method-response/#statusCode) property to an HTTP status code\. The following AWS CLI command creates a method response of `200`\.

```
aws apigateway put-method-response \
       --region us-west-2  \
       --rest-api-id vaz7da96z6 \ 
       --resource-id 6sxz2j \
       --http-method GET \
       --status-code 200
```

## Set up method response parameters<a name="setup-method-response-parameters"></a>

Method response parameters define which headers the client receives in response to the associated method request\. Response parameters also specify a target to which API Gateway maps an integration response parameter, according to mappings prescribed in the API method's integration response\. 

To set up the method response parameters, add to the [https://docs.aws.amazon.com/apigateway/api-reference/resource/method-response/#responseParameters](https://docs.aws.amazon.com/apigateway/api-reference/resource/method-response/#responseParameters) map of `MethodResponse` key\-value pairs of the `"{parameter-name}":"{boolean}"` format\. The following CLI command shows an example of setting the `my-header` header, the `petId` path variable, and the `query` query parameter as the mapping targets:

```
aws apigateway put-method-response \
        --region us-west-2 \
        --rest-api-id vaz7da96z6 \ 
        --resource-id 6sxz2j \
        --http-method GET \
        --status-code 200  \
        --response-parameters method.request.header.my-header=false,method.request.path.petId=true,method.request.querystring.query=false
```

## Set up method response models<a name="setup-method-response-models"></a>

 A method response model defines a format of the method response body\. Before setting up the response model, you must first create the model in API Gateway\. To do so, you can call the `[create\-model](https://docs.aws.amazon.com/cli/latest/reference/apigateway/create-model.html)` command\. The following example shows how to create a `PetStorePet` model to describe the body of the response to the `GET /pets/{petId}` method request\.

```
aws apigateway create-model \
    --region us-west-2 \
    --rest-api-id vaz7da96z6 \
    --content-type application/json \
    --name PetStorePet \
    --schema '{ \
                  "$schema": "http://json-schema.org/draft-04/schema#", \
                  "title": "PetStorePet", \
                  "type": "object", \
                  "properties": { \
                    "id": { "type": "number" }, \
                    "type": { "type": "string" }, \
                    "price": { "type": "number" } \
                  } \
              }'
```

The result is created as an API Gateway [https://docs.aws.amazon.com/apigateway/api-reference/resource/model/](https://docs.aws.amazon.com/apigateway/api-reference/resource/model/) resource\.

To set up the method response models to define the payload format, add the "application/json":"PetStorePet" key\-value pair to the [https://docs.aws.amazon.com/apigateway/api-reference/resource/method-response/#responseModels](https://docs.aws.amazon.com/apigateway/api-reference/resource/method-response/#responseModels) map of [https://docs.aws.amazon.com/apigateway/api-reference/resource/method-response/](https://docs.aws.amazon.com/apigateway/api-reference/resource/method-response/) resource\. The following AWS CLI command of `put-method-response` shows how this is done: 

```
aws apigateway put-method-response \
        --region us-west-2 \
        --rest-api-id vaz7da96z6 \ 
        --resource-id 6sxz2j \
        --http-method GET \
        --status-code 200  \
        --request-parameters method.request.header.my-header=false,method.request.path.petId=true,method.request.querystring.query=false
        --request-models '{"application/json":"PetStorePet"}'
```

Setting up a method response model is necessary when you generate a strongly typed SDK for the API\. It ensures that the output is cast into an appropriate class in Java or Objective\-C\. In other cases, setting a model is optional\.