# Use a mapping template to override an API's request and response parameters and status codes<a name="apigateway-override-request-response-parameters"></a>

Standard API Gateway [parameter and response code mapping templates](models-mappings.md) allow you to map parameters one\-to\-one and map a family of integration response status codes \(matched by a regular expression\) to a single response status code\. Mapping template overrides provides you with the flexibility to perform many\-to\-one parameter mappings; override parameters after standard API Gateway mappings have been applied; conditionally map parameters based on body content or other parameter values; programmatically create new parameters on the fly; and override status codes returned by your integration endpoint\. Any type of request parameter, response header, or response status code may be overridden\.

Following are example uses for a mapping template override:
+ To create a new header \(or overwrite an existing header\) as a concatenation of two parameters
+ To override the response code to a success or failure code based on the contents of the body
+ To conditionally remap a parameter based on its contents or the contents of some other parameter
+ To iterate over the contents of a json body and remap key value pairs to headers or query strings

To create a mapping template override, use one or more of the following [`$context` variables](api-gateway-mapping-template-reference.md#context-variable-reference) in a [mapping template](models-mappings.md):


| Request body mapping template | Response body mapping template | 
| --- | --- | 
| $context\.requestOverride\.header\.header\_name | $context\.responseOverride\.header\.header\_name | 
| $context\.requestOverride\.path\.path\_name | $context\.responseOverride\.status | 
| $context\.requestOverride\.querystring\.querystring\_name |  | 

**Note**  
Mapping template overrides cannot be used with proxy integration endpoints, which lack data mappings\. For more information about integration types, see [Choose an API Gateway API integration type](api-gateway-api-integration-types.md)\.

**Important**  
Overrides are final\. An override may only be applied to each parameter once\. Trying to override the same parameter multiple times will result in `5XX` responses from Amazon API Gateway\. If you must override the same parameter multiple times throughout the template, we recommend creating a variable and applying the override at the end of the template\. Note that the template is applied only after the entire template is parsed\. See [Tutorial: Override an API's request parameters and headers with the API Gateway console](#apigateway-override-request-response-parameters-override-request)\.

The following tutorials show how to create and test a mapping template override in the API Gateway console\. These tutorials use the [PetStore sample API](api-gateway-create-api-from-example.md) as a starting point\. Both tutorials assume that you have already created the [PetStore sample API](api-gateway-create-api-from-example.md)\.

**Topics**
+ [Tutorial: Override an API's response status code with the API Gateway console](#apigateway-override-request-response-parameters-override-response)
+ [Tutorial: Override an API's request parameters and headers with the API Gateway console](#apigateway-override-request-response-parameters-override-request)
+ [Examples: Override an API's request parameters and headers with the API Gateway CLI](#apigateway-override-request-response-parameters-cli)
+ [Example: Override an API's request parameters and headers using the SDK for JavaScript](#apigateway-override-request-response-parameters-javascript-sdk)

## Tutorial: Override an API's response status code with the API Gateway console<a name="apigateway-override-request-response-parameters-override-response"></a>

To retrieve a pet using the PetStore sample API, you use the API method request of `GET /pets/{petId}`, where `{petId}` is a path parameter that can take a number at run time\.

In this tutorial, you'll override this `GET` method's response code by creating a mapping template that maps `$context.responseOverride.status` to `400` when an error condition is detected\.

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Under **APIs**, choose the PetStore API\.

1. In the **Resources** column, choose the `GET` method under `/{petId}`\.

1. In the **Client** box, choose **Test**\.

1. Type `-1` for **\{petId\}** and choose **Test**\.

   In the results, you'll notice two things:

   First, the **Response Body** indicates an out\-of\-range error:

   ```
   {
     "errors": [
       {
         "key": "GetPetRequest.petId",
         "message": "The value is out of range."
       }
     ]
   }
   ```

   Second, the last line under **Logs** box ends with: `Method completed with status: 200`\.

1. Go back to **Method Execution**\. Choose **Integration Response**, and then choose the arrow next to **200**\.

1. In the **Mapping Templates** section, choose **Add mapping template**\.

1. For **Content Type**, type **application/json**, and then choose the check mark icon to save the choice\.

1. Copy the following code into the template area:

   ```
   #set($inputRoot = $input.path('$'))
   $input.json("$")
   #if($inputRoot.toString().contains("error"))
   #set($context.responseOverride.status = 400)
   #end
   ```

1. Choose **Save**\.

1. Go back to **Method Execution**

1. In the **Client** box, choose **Test**\.

1. Type `-1` for **\{petId\}** and choose **Test**\.

   In the results, the **Response Body** indicates an out\-of\-range error:

   ```
   {
     "errors": [
       {
         "key": "GetPetRequest.petId",
         "message": "The value is out of range."
       }
     ]
   }
   ```

   However, the last line under **Logs** box now ends with: `Method completed with status: 400`\.

## Tutorial: Override an API's request parameters and headers with the API Gateway console<a name="apigateway-override-request-response-parameters-override-request"></a>

In this tutorial, you'll override the `GET` method's request header code by creating a mapping template that maps `$context.requestOverride.header.header_name` to a new header that combines two other headers\.

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Under **APIs**, choose the PetStore API\.

1. In the **Resources** column, choose the `GET` method under `/pets`\.

1. Choose **Method Request**\.

1. Create a parameter as follows:

   1. Expand **HTTP Request Headers**\.

   1. Choose **Add header**\.

   1. Under **Name**, type **header1**\.

   1. Choose the check mark icon to save your choice\.

   Repeat the process to create a second header called `header2`\.

1. Go back to **Method Execution**

1. Choose **Integration Request**\.

1. Expand **HTTP Headers**\. You'll see the two headers you created, `header1` and `header2`, along with their default mappings \(under **Mapped from**\)\.

1. Expand **Mapping Templates**\.

1. Choose **Add mapping template**\.

1. For **Content Type**, type **application/json**, and then choose the check mark icon to save the choice\.

1. A popup will appear that says, **Note: This template can map headers and body\.**

   Choose **Yes, secure this integration**\.

1. Copy the following code into the template area:

   ```
   #set($header1Override = "foo")
   #set($header3Value = "$input.params('header1')$input.params('header2')")
   $input.json("$")
   #set($context.requestOverride.header.header3 = $header3Value)
   #set($context.requestOverride.header.header1 = $header1Override)
   #set($context.requestOverride.header.multivalueheader=[$header1Override, $header3Value])
   ```

1. Choose **Save**\.

1. Go back to **Method Execution**

1. In the **Client** box, choose **Test**\.

1. Under **Headers** for **\{pets\}**, copy the following code:

   ```
   header1:header1Val
   header2:header2Val
   ```

1. Choose **Test**\.

   In the Logs, you should see an entry that includes this text:

   ```
   Endpoint request headers: {header3=header1Valheader2Val, 
   header2=header2Val, header1=foo, x-amzn-apigateway-api-id=<api-id>,
   Accept=application/json, multivalueheader=foo,header1Valheader2Val}
   ```

## Examples: Override an API's request parameters and headers with the API Gateway CLI<a name="apigateway-override-request-response-parameters-cli"></a>

The following CLI example shows how to use the `put-integration` command to override a response code:

```
aws apigateway put-integration --rest-api-id <API_ID> --resource-id <PATH_TO_RESOURCE_ID> --http-method <METHOD> 
            --type <INTEGRATION_TYPE> --request-templates <REQUEST_TEMPLATE_MAP>
```

where *<REQUEST\_TEMPLATE\_MAP>* is a map from content type to a string of the template to apply\. The structure of that map is as follows:

```
Content_type1=template_string,Content_type2=template_string
```

or, in JSON syntax:

```
{"content_type1": "template_string"
...}
```

The following example shows how to use the `put-integration-response` command to override an API's response code:

```
aws apigateway put-integration-response --rest-api-id <API_ID> --resource-id <PATH_TO_RESOURCE_ID> --http-method <METHOD> 
            --status-code <STATUS_CODE> --response-templates <RESPONSE_TEMPLATE_MAP>
```

where *<RESPONSE\_TEMPLATE\_MAP>* has the same format as *<REQUEST\_TEMPLATE\_MAP>* above\.

## Example: Override an API's request parameters and headers using the SDK for JavaScript<a name="apigateway-override-request-response-parameters-javascript-sdk"></a>

The following example shows how to use the `put-integration` command to override a response code:

**Request:**

```
var params = {
  httpMethod: 'STRING_VALUE', /* required */
  resourceId: 'STRING_VALUE', /* required */
  restApiId: 'STRING_VALUE', /* required */
  type: HTTP | AWS | MOCK | HTTP_PROXY | AWS_PROXY, /* required */
  requestTemplates: {
    '<Content_type>': 'TEMPLATE_STRING',
    /* '<String>': ... */
  },
};
apigateway.putIntegration(params, function(err, data) {
  if (err) console.log(err, err.stack); // an error occurred
  else     console.log(data);           // successful response
});
```

**Response:**

```
var params = {
  httpMethod: 'STRING_VALUE', /* required */
  resourceId: 'STRING_VALUE', /* required */
  restApiId: 'STRING_VALUE', /* required */
  statusCode: 'STRING_VALUE', /* required */
  responseTemplates: {
    '<Content_type>': 'TEMPLATE_STRING',
    /* '<String>': ... */
  },
};
apigateway.putIntegrationResponse(params, function(err, data) {
  if (err) console.log(err, err.stack); // an error occurred
  else     console.log(data);           // successful response
});
```