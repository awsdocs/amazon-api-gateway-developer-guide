# Set up request and response data mappings using the API Gateway console<a name="how-to-method-settings-execution-console"></a>

To use the API Gateway console to define the API's integration request/response, follow these instructions\.

**Note**  
These instructions assume you have already completed the steps in [Set up an API integration request using the API Gateway console](how-to-method-settings-console.md)\.

1. With the method selected in the **Resources** pane, in the **Method Execution** pane, choose **Integration Request**\.

1. For an HTTP proxy or an AWS service proxy, to associate a path parameter, a query string parameter, or a header parameter defined in the integration request with a corresponding path parameter, query string parameter, or header parameter in the method request of the HTTP proxy or AWS service proxy, do the following:

   1. Choose the arrow next to **URL Path Parameters**, **URL Query String Parameters**, or **HTTP Headers** respectively, and then choose **Add path**, **Add query string**, or **Add header**, respectively\.

   1. For **Name**, type the name of the path parameter, query string parameter, or header parameter in the HTTP proxy or AWS service proxy\. 

   1. For **Mapped from**, type the mapping value for the path parameter, query string parameter, or header parameter\. Use one of the following formats:
      + **method\.request\.path\.*parameter\-name*** for a path parameter named *parameter\-name* as defined in the **Method Request** page\.
      + **method\.request\.querystring\.*parameter\-name*** for a query string parameter named *parameter\-name* as defined in the **Method Request** page\.
      + **method\.request\.multivaluequerystring\.*parameter\-name*** for a multi\-valued query string parameter named *parameter\-name* as defined in the **Method Request** page\.
      + **method\.request\.header\.*parameter\-name*** for a header parameter named *parameter\-name* as defined in the **Method Request** page\.

         Alternatively, you can set a literal string value \(enclosed by a pair of single quotes\) to an integration header\.  
      + **method\.request\.multivalueheader\.*parameter\-name*** for a multi\-valued header parameter named *parameter\-name* as defined in the **Method Request** page\.

   1. Choose **Create**\. \(To delete a path parameter, query string parameter, or header parameter, choose **Cancel** or **Remove** next to the parameter you want to delete\.\)

1. In the **Body Mapping Templates** area, choose an option for **Request body passthrough** to configure how the method request body of an unmapped content type will be passed through the integration request without transformation to the Lambda function, HTTP proxy, or AWS service proxy\. There are three options: 
   + Choose **When no template matches the request Content\-Type header** if you want the method request body to pass through the integration request to the backend without transformation when the method request content type does not match any content types associated with the mapping templates, as defined in the next step\.
**Note**  
When calling the API Gateway API, you choose this option by setting `WHEN_NO_MATCH` as the `passthroughBehavior` property value on the [Integration](https://docs.aws.amazon.com/apigateway/api-reference/resource/integration/) resource\.
   + Choose **When there are no templates defined \(recommended\)** if you want the method request body to pass through the integration request to the backend without transformation when no mapping template is defined in the integration request\. If a template is defined when this option is selected, the method request of an unmapped content type will be rejected with an HTTP 415 Unsupported Media Type response\.
**Note**  
When calling the API Gateway API, you choose this option by setting `WHEN_NO_TEMPLATE` as the `passthroughBehavior` property value on the [Integration](https://docs.aws.amazon.com/apigateway/api-reference/resource/integration/) resource\.
   + Choose **Never** if you do not want the method request to pass through when either the method request content type does not match any content type associated with the mapping templates defined in the integration request or no mapping template is defined in the integration request\. The method request of an unmapped content type will be rejected with an HTTP 415 Unsupported Media Type response\. 
**Note**  
When calling the API Gateway API, you choose this option by setting `NEVER` as the `passthroughBehavior` property value on the [Integration](https://docs.aws.amazon.com/apigateway/api-reference/resource/integration/) resource\.

   For more information about the integration passthrough behaviors, see [Integration passthrough behaviors](integration-passthrough-behaviors.md)\.

1.  To define a mapping template for an incoming request, choose **Add mapping template** under **Content\-Type**\. Type a content type \(e\.g\., **application/json**\) in the input text box and then choose the check mark icon to save the input\. Then, type the mapping template manually or choose **Generate template** to create one from a model template\. For more information, see [Working with models and mapping templates](models-mappings.md)\. 

1.  You can map an integration response from the backend to a method response of the API returned to the calling app\. This includes returning to the client selected response headers from the available ones from the backend, transforming the data format of the backend response payload to an API\-specified format\. You can specify such mapping by configuring **Method Response** and **Integration Response** from the **Method Execution** page\. 

   1. In the **Method Execution** pane, choose **Integration Response**\. Choose either the arrow next to **200** to specify settings for a 200 HTTP response code from the method, or choose **Add integration response** to specify settings for any other HTTP response status code from the method\.

   1. For **Lambda error regex** \(for a Lambda function\) or **HTTP status regex** \(for an HTTP proxy or AWS service proxy\), type a regular expression to specify which Lambda function error strings \(for a Lambda function\) or HTTP response status codes \(for an HTTP proxy or AWS service proxy\) map to this output mapping\. For example, to map all 2xx HTTP response status codes from an HTTP proxy to this output mapping, type "**2\\\\d\{2\}**" for **HTTP status regex**\. To return an error message containing "Invalid Request" from a Lambda function to a `400 Bad Request` response, type "**\.\*Invalid request\.\***" as the **Lambda error regex** expression\. On the other hand, to return `400 Bad Request` for all unmapped error messages from Lambda, type "**\(\\n\|\.\)\+**" in **Lambda error regex**\. This last regular expression can be used for the default error response of an API\.
**Note**  
API Gateway uses Java pattern\-style regexes for response mapping\. For more information, see [Pattern](https://docs.oracle.com/javase/8/docs/api/java/util/regex/Pattern.html) in the Oracle documentation\.   
The error patterns are matched against the entire string of the `errorMessage` property in the Lambda response, which is populated by `callback(errorMessage)` in Node\.js or by `throw new MyException(errorMessage)` in Java\. Also, escaped characters are unescaped before the regular expression is applied\.   
 If you use '\.\+' as the selection pattern to filter responses, be aware that it may not match a response containing a newline \('\\n'\) character\.   

   1. If enabled, for **Method response status**, choose the HTTP response status code you defined in the **Method Response** page\.

   1. For **Header Mappings**, for each header you defined for the HTTP response status code in the **Method Response** page, specify a mapping value by choosing **Edit**\. For **Mapping value**, use one of the following formats:
      + **integration\.response\.multivalueheaders\.*header\-name*** where *header\-name* is the name of a multi\-valued response header from the backend\.

        For example, to return the backend response's `Date` header as an API method's response's `Timestamp` header, the **Response header** column will contain a **Timestamp** entry, and the associated **Mapping value** should be set to **integration\.response\.multivalueheaders\.Date**\.
      + **integration\.response\.header\.*header\-name*** where *header\-name* is the name of a single\-valued response header from the backend\.

        For example, to return the backend response's `Date` header as an API method's response's `Timestamp` header, the **Response header** column will contain a **Timestamp** entry, and the associated **Mapping value** should be set to **integration\.response\.header\.Date**\.

   1. In the **Template Mappings** area, next to **Content type**, choose **Add**\. In the **Content type** box, type the content type of the data that will be passed from the Lambda function, HTTP proxy, or AWS service proxy to the method\. Choose **Update**\.

   1. Select **Output passthrough** if you want the method to receive, but not modify, the data from the Lambda function, HTTP proxy, or AWS service proxy\.

   1. If **Output passthrough** is cleared, for **Output mapping**, specify the output mapping template you want the Lambda function, HTTP proxy, or AWS service proxy to use to send data to the method\. You can either type the mapping template manually or choose a model from **Generate template from model**\.

   1. Choose **Save**\.