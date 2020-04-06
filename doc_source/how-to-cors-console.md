# Enable CORS on a resource using the API Gateway console<a name="how-to-cors-console"></a>

You can use the API Gateway console to enable CORS support for one or all methods on a REST API resource that you have created\.

**Important**  
Resources can contain child resources\. Enabling CORS support for a resource and its methods does not recursively enable it for child resources and their methods\.

**Enable CORS support on a REST API resource**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Choose the API from the **APIs** list\.

1. Choose a resource under **Resources**\. This will enable CORS for all the methods on the resource\.

   Alternatively, you could choose a method under the resource to enable CORS for just this method\.

1. Choose **Enable CORS** from the **Actions** drop\-down menu\.  
![\[Choose enable CORS\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/amazon-api-gateway-enable-cors.png)

1.  In the **Enable CORS** form, do the following: 

   1.  In the **Access\-Control\-Allow\-Headers** input field, type a static string of a comma\-separated list of headers that the client must submit in the actual request of the resource\. Use the console\-provided header list of `'Content-Type,X-Amz-Date,Authorization,X-Api-Key,X-Amz-Security-Token'` or specify your own headers\. 

   1.  Use the console\-provided value of `'*'` as the **Access\-Control\-Allow\-Origin** header value to allow access requests from all origins, or specify origins to be permitted to access the resource\. 

   1. Choose **Enable CORS and replace existing CORS headers**\.  
![\[Choose which headers are allowed\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/amazon-api-gateway-enable-cors-resources.png)
**Important**  
 When applying the above instructions to the `ANY` method in a proxy integration, any applicable CORS headers will not be set\. Instead, your backend must return the applicable CORS headers, such as `Access-Control-Allow-Origin`\. 

1. In **Confirm method changes**, choose **Yes, overwrite existing values** to confirm the new CORS settings\.  
![\[Confirm overwrite of existing values\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/amazon-api-gateway-enable-cors-confirm-method-overwrite.png)

After CORS is enabled on the `GET` method, an `OPTIONS` method is added to the resource, if it is not already there\. The `200` response of the `OPTIONS` method is automatically configured to return the three `Access-Control-Allow-*` headers to fulfill preflight handshakes\. In addition, the actual \(`GET`\) method is also configured by default to return the `Access-Control-Allow-Origin` header in its 200 response as well\. For other types of responses, you will need to manually configure them to return `Access-Control-Allow-Origin'` header with '\*' or specific origins, if you do not want to return the `Cross-origin access` error\.

After you enable CORS support on your resource, you must deploy or redeploy the API for the new settings to take effect\. For more information, see [Deploying a REST API from the API Gateway console](how-to-deploy-api-with-console.md)\.