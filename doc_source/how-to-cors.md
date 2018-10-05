# Enable CORS for an API Gateway Resource<a name="how-to-cors"></a>

When your API's resources receive requests from a domain other than the API's own domain, you must enable cross\-origin resource sharing \(CORS\) for selected methods on the resource\. This amounts to having your API respond to the `OPTIONS` preflight request with at least the following CORS\-required response headers: 
+ `Access-Control-Allow-Methods`
+ `Access-Control-Allow-Headers`
+ `Access-Control-Allow-Origin`

 In API Gateway you enable CORS by setting up an `OPTIONS` method with the mock integration type to return the preceding response headers \(with static values discussed in the following\) as the method response headers\. In addition, the actual CORS\-enabled methods must also return the `Access-Control-Allow-Origin:'request-originating server addresses'` header in at least its 200 response\. You can replace the static value of specific `request-originating server addresses` with `*` to indicate any servers\. However, you should be careful of enabling such a broad support and do so only when you fully understand the consequences\.

With Lambda, AWS or HTTP integrations, you can leverage API Gateway to set up the required headers using the method response and integration response\. For Lambda or HTTP [proxy integrations](api-gateway-set-up-simple-proxy.md), you can still set up the required OPTIONS response headers in API Gateway\. However, you must rely on the back end to return the `Access-Control-Allow-Origin` headers because the integration response is disabled for the proxy integration\.

**Tip**  
You must set up an OPTIONS method to handle preflight requests to support CORS\. However, OPTIONS methods are optional if 1\) an API resource exposes only the GET, HEAD or POST methods and 2\) the request payload content type is `application/x-www-form-urlencoded`, `multipart/form-data` or `text/plain` and 3\) the request does not contain any custom headers\. When possible, we recommend to use OPTIONS method to enable CORS in your API\.

 This section describes how to enable CORS for a method in API Gateway using the API Gateway console or the API Gateway [Import an API into API Gateway](api-gateway-import-api.md)\. 

**Topics**
+ [Prerequisites](#how-to-cors-prerequisites)
+ [Enable CORS on a Resource Using the API Gateway Console](#how-to-cors-console)
+ [Enable CORS on a Resource Using the API Gateway Import API](enable-cors-for-resource-using-swagger-importer-tool.md)

## Prerequisites<a name="how-to-cors-prerequisites"></a>
+  You must have the method available in API Gateway\. For instructions on how to create and configure a method, see [Build an API with HTTP Custom Integration](api-gateway-create-api-step-by-step.md)\. 

## Enable CORS on a Resource Using the API Gateway Console<a name="how-to-cors-console"></a>

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1.  In the API Gateway console, choose an API under **APIs**\. 

1. Choose a resource under **Resources**\. This will enable CORS for all the methods on the resource\.

   Alternatively, you could choose a method under the resource to enable CORS for just this method\.

1. Choose **Enable CORS** from the **Actions** drop\-down menu\.  
![\[Choose Enable CORS\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/amazon-api-gateway-enable-cors.png)

1.  In the **Enable CORS** form, do the following: 

   1.  In the **Access\-Control\-Allow\-Headers** input field, type a static string of a comma\-separated list of headers that the client must submit in the actual request of the resource\. Use the console\-provided header list of `'Content-Type,X-Amz-Date,Authorization,X-Api-Key,X-Amz-Security-Token'` or specify your own headers\. 

   1.  Use the console\-provided value of `'*'` as the **Access\-Control\-Allow\-Origin** header value to allow access requests from all domains, or specify a named domain to all access requests from the specified domain\. 

   1. Choose **Enable CORS and replace existing CORS headers**\.  
![\[Choose which headers are allowed\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/amazon-api-gateway-enable-cors-resources.png)
**Note**  
 When applying the above instructions to the `ANY` method in a proxy integration, any applicable CORS headers will not be set\. Instead, you rely on the integration backend to return the applicable CORS headers, such as `Access-Control-Allow-Origin` 

1. In **Confirm method changes**, choose **Yes, overwrite existing values** to confirm the new CORS settings\.  
![\[Confirm overwrite of existing values\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/amazon-api-gateway-enable-cors-confirm-method-overwrite.png)

After CORS is enabled on the GET method, an OPTIONS method is added to the resource, if it is not already there\. The `200` response of the OPTIONS method is automatically configured to return the three `Access-Control-Allow-*` headers to fulfill preflight handshakes\. In addition, the actual \(GET\) method is also configured by default to return the `Access-Control-Allow-Origin` header in its 200 response as well\. For other types of responses, you will need to manually configure them to return `Access-Control-Allow-Origin'` header with '\*' or specific origin domain names, if you do not want to return the Cross\-origin access error\.

As with any updates of your API, you must deploy or redeploy the API for the new settings to take effect\.