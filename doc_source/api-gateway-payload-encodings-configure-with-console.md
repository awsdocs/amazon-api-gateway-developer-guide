# Enabling binary support using the API Gateway console<a name="api-gateway-payload-encodings-configure-with-console"></a>

The section explains how to enable binary support using the API Gateway console\. As an example, we use an API that is integrated with Amazon S3\. We focus on the tasks to set the supported media types and to specify how the payload should be handled\. For detailed information on how to create an API integrated with Amazon S3, see [Tutorial: Create a REST API as an Amazon S3 proxy in API Gateway](integrating-api-with-aws-services-s3.md)\.

**To enable binary support by using the API Gateway console**

1. Set binary media types for the API:

   1. Create a new API or choose an existing API\. For this example, we name the API `FileMan`\.

   1. Under the selected API in the primary navigation panel, choose **Settings**\.

   1. In the **Settings** pane, choose **Add Binary Media Type** in the **Binary Media Types** section\.

   1. Type a required media type, for example, `image/png`, in the input text field\. If needed, repeat this step to add more media types\. To support all binary media types, specify `/`\.

   1. Choose **Save Changes**\.  
![\[Add binary support in the API Gateway console.\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/binary-support-set-binary-media-types-on-api.png)

1. Set how message payloads are handled for the API method:

   1. Create a new or choose an existing resource in the API\. For this example, we use the `/{folder}/{item}` resource\.

   1. Create a new or choose an existing method on the resource\. As an example, we use the `GET /{folder}/{item}` method integrated with the `Object GET` action in Amazon S3\. 

   1. In **Content Handling**, choose an option\.   
![\[Set up the GET method in the API Gateway console.\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/binary-support-set-content-handling-on-method.png)

      Choose **Passthrough** if you don't want to convert the body when the client and backend accepts the same binary format\. Choose **Convert to text \(if needed\)** to convert the binary body to a base64\-encoded string when, for example, the backend requires that a binary request payload is passed in as a JSON property\. And choose **Convert to binary \(if needed\)** when the client submits a base64\-encoded string and the backend requires the original binary format, or when the endpoint returns a base64\-encoded string and the client accepts only the binary output\.

   1. Preserve the incoming request's `Accept` header in the integration request\. You should do this if you've set `contentHandling` to `passthrough` and want to override that setting at runtime\.  
![\[Keep the Accept header in the integration request.\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/binary-support-preserve-incoming-accept-header.png)

   1. Enable the passthrough behavior on the request body\.  
![\[Enable passthrough behavior for the request body.\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/binary-support-ensure-payload-passthrough-on-method.png)

   1. For conversion to text, define a mapping template to put the base64\-encoded binary data into the required format\.  
![\[Mapping template for the convert-to-text option.\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/binary-support-convert-to-text-mapping-template.png)

      The format of this mapping template depends on the endpoint requirements of the input\.