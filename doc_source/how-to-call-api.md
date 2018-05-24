# Invoking an API in Amazon API Gateway<a name="how-to-call-api"></a>

Calling a deployed API involves submitting requests to the API Gateway component service for API execution, known as `execute-api`\. The root URL of such requests is of the following format: 

```
https://{restapi_id}.execute-api.{region}.amazonaws.com/{stage_name}/
```

where *\{restapi\_id\}* is the API identifier, *\{region\}* is the API deployed region, and *\{stage\_name\}* is the stage name of an API deployment\. 

You can find this root URL in the given **Stage Editor**\. It is listed as the **Invoke URL** at the top\. If the API's root resource exposes a GET method without requiring user authentication, you can call the method by clicking the **Invoke URL** link\. You can also construct this root URL by combining the `host` and `basePath` fields of an exported Swagger definition file of the API\. 

If an API permits anonymous access, you can use any web browser to invoke any GET\-method calls by copying and pasting an appropriate invocation URL to the browser's address bar\. For other methods or any authentication\-required calls, the invocation will be more involved because you must specify a payload or sign the requests\. You can handle these in a script behind an HTML page or in a client app using one of the AWS SDKs\.

For testing, you can use the API Gateway console to call an API using the API Gateway's TestInvoke feature, which bypasses the Invoke URL and allows API testing before the API is deployed\. Alternatively, you can use the [Postman](http://www.getpostman.com/) app to test a successfully deployed API, without writing a script or a client\.

**Note**  
 Query string parameter values in an invocation URL cannot contain `%%`\. 

**Topics**
+ [Obtain an API's Invoke URL in the API Gateway Console](#how-to-call-api-console)
+ [Use the API Gateway Console to Test a Method](how-to-test-method.md)
+ [Use Postman to Call an API](how-to-use-postman-to-call-api.md)
+ [Call API through Generated SDKs](how-to-call-api-using-generated-sdk.md)
+ [Call API through AWS Amplify JavaScript Library](how-to-call-api-using-aws-amplify-javascript-library.md)
+ [Trace API Management and Invocation](monitoring_overview.md)

**Important**  
 You must have already deployed the API in API Gateway\. Follow the instructions in [Deploying an API in Amazon API Gateway](how-to-deploy-api.md)\. 

## Obtain an API's Invoke URL in the API Gateway Console<a name="how-to-call-api-console"></a>

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. In the box that contains the name of the API you want to call, choose **Stages**\.

1. In the **Stages** pane, choose the name of the stage\.

1. The URL displayed next to **Invoke URL** should look something like this, where *my\-api\-id* is the identifier API Gateway assigns to your API, *region\-id* is the AWS region identifier \(for example, `us-east-1` \) where you deployed your API, and *stage\-name* is the name of the stage for the API you want to call:

   ```
   https://my-api-id.execute-api.region-id.amazonaws.com/stage-name/{resourcePath}
   ```

Depending on the method type you want to call and the tool you want to use, copy this URL to your clipboard, and then paste and modify it to call the API from a web browser, a web debugging proxy tool or the cURL command\-line tool, or from your own API\. 

If you are not familiar with which method to call or the format you must use to call it, browse the list of available methods by following the instructions in [View a Methods List in API Gateway](maintain-api-using-console.md#how-to-view-methods-list)\.

To call the method directly from the API Gateway console, see [Use the Console to Test a Method](how-to-test-method.md)\.

For more options, contact the API owner\.