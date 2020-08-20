# Tutorial: Create a REST API as an Amazon S3 proxy in API Gateway<a name="integrating-api-with-aws-services-s3"></a>

As an example to showcase using a REST API in API Gateway to proxy Amazon S3, this section describes how to create and configure a REST API to expose the following Amazon S3 operations: 
+ Expose GET on the API's root resource to [list all of the Amazon S3 buckets of a caller](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTServiceGET.html)\.
+ Expose GET on a Folder resource to [view a list of all of the objects in an Amazon S3 bucket](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketGET.html)\.
+ Expose PUT on a Folder resource to [add a bucket to Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUT.html)\.
+ Expose DELETE on a Folder resource to [remove a bucket from Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketDELETE.html)\.
+ Expose GET on a Folder/Item resource to [view or download an object from an Amazon S3 bucket](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectGET.html)\.
+ Expose PUT on a Folder/Item resource to [upload an object to an Amazon S3 bucket](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPUT.html)\.
+ Expose HEAD on a Folder/Item resource to [get object metadata in an Amazon S3 bucket](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectHEAD.html)\.
+ Expose DELETE on a Folder/Item resource to [remove an object from an Amazon S3 bucket](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectDELETE.html)\.

**Note**  
 To integrate your API Gateway API with Amazon S3, you must choose a region where both the API Gateway and Amazon S3 services are available\. For region availability, see [Amazon API Gateway Endpoints and Quotas](https://docs.aws.amazon.com/general/latest/gr/apigateway.html)\. 

 You may want to import the sample API as an Amazon S3 proxy, as shown in [OpenAPI definitions of the sample API as an Amazon S3 proxy](api-as-s3-proxy-export-swagger-with-extensions.md)\. For instructions on how to import an API using the OpenAPI definition, see [Configuring a REST API using OpenAPI](api-gateway-import-api.md)\. 

 To use the API Gateway console to create the API, you must first sign up for an AWS account\. 

If you do not have an AWS account, complete the following steps to create one\.

**To sign up for an AWS account**

1. Open [https://portal\.aws\.amazon\.com/billing/signup](https://portal.aws.amazon.com/billing/signup)\.

1. Follow the online instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a verification code on the phone keypad\.

**Topics**
+ [Set up IAM permissions for the API to invoke Amazon S3 actions](#api-as-s3-proxy-iam-permissions)
+ [Create API resources to represent Amazon S3 resources](#api-as-s3-proxy-create-resources)
+ [Expose an API method to list the caller's Amazon S3 buckets](#api-root-get-as-s3-get-service)
+ [Expose API methods to access an Amazon S3 bucket](#api-folder-operations-as-s3-bucket-actions)
+ [Expose API methods to access an Amazon S3 object in a bucket](#api-items-in-folder-as-s3-objects-in-bucket)
+ [Call the API using a REST API client](#api-as-s3-proxy-test-using-postman)
+ [OpenAPI definitions of the sample API as an Amazon S3 proxy](api-as-s3-proxy-export-swagger-with-extensions.md)

## Set up IAM permissions for the API to invoke Amazon S3 actions<a name="api-as-s3-proxy-iam-permissions"></a>

To allow the API to invoke required Amazon S3 actions, you must have appropriate IAM policies attached to an IAM role\. The next section describes how to verify and to create, if necessary, the required IAM role and policies\. 

 For your API to view or list Amazon S3 buckets and objects, you can use the IAM\-provided AmazonS3ReadOnlyAccess policy in the IAM role\. The ARN of this policy is `arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess`, which is as shown as follows: 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:Get*",
                "s3:List*"
            ],
            "Resource": "*"
        }
    ]
}
```

 This policy document states that any of the Amazon S3 `Get*` and `List*` actions can be invoked on any of the Amazon S3 resources\. 

 For your API to update Amazon S3 buckets and objects , you can use a custom policy for any of the Amazon S3 `Put*` actions as shown as follows: 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "s3:Put*",
            "Resource": "*"
        }
    ]
}
```

 For your API to work with Amazon S3 `Get*`, `List*` and `Put*` actions, you can add the above read\-only and put\-only policies to the IAM role\.

 For your API to invoke the Amazon S3 `Post*` actions, you must use an Allow policy for the `s3:Post*` actions in the IAM role\. For a complete list of Amazon S3 actions, see [Specifying Amazon S3 Permissions in a Policy](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html)\. 

 For your API to create, view, update, and delete buckets and objects in Amazon S3, you can use the IAM \-provided AmazonS3FullAccess policy in the IAM role\. The ARN is `arn:aws:iam::aws:policy/AmazonS3FullAccess`\. 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "s3:*",
            "Resource": "*"
        }
    ]
}
```

 

 Having chosen the desired IAM policies to use, create an IAM role and attach to it the policies\. The resulting IAM role must contain the following trust policy for API Gateway to assume this role at runtime\. 

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "",
      "Effect": "Allow",
      "Principal": {
        "Service": "apigateway.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

 When using the IAM console to create the role, choose the **Amazon API Gateway** role type to ensure that this trust policy is automatically included\. 

## Create API resources to represent Amazon S3 resources<a name="api-as-s3-proxy-create-resources"></a>

We will use the API's root \(`/`\) resource as the container of an authenticated caller's Amazon S3 buckets\. We will also create a `Folder` and `Item` resources to represent a particular Amazon S3 bucket and a particular Amazon S3 object, respectively\. The folder name and object key will be specified, in the form of path parameters as part of a request URL, by the caller\. 

**Note**  
When accessing objects whose object key includes `/` or any other special character, the character needs to be URL encoded\. For example, `test/test.txt` should be encoded to `test%2Ftest.txt`\.

**To create an API resource that exposes the Amazon S3 service features**

1.  In the API Gateway console, create an API named **MyS3**\. This API's root resource \(**/**\) represents the Amazon S3 service\. 

1.  Under the API's root resource, create a child resource named **Folder** and set the required **Resource Path** as **/\{folder\}**\. 

1.  For the API's **Folder** resource, create an **Item** child resource\. Set the required **Resource Path** as **/\{item\}**\.   
![\[Create an API in API Gateway as an Amazon S3 proxy\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/aws_proxy_s3_create_api-resources.png)

## Expose an API method to list the caller's Amazon S3 buckets<a name="api-root-get-as-s3-get-service"></a>

Getting the list of Amazon S3 buckets of the caller involves invoking the [GET Service](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTServiceGET.html) action on Amazon S3\. On the API's root resource, \(**/**\), create the GET method\. Configure the GET method to integrate with the Amazon S3, as follows\. 

**To create and initialize the API's `GET /` method**

1. Choose **Create method** on the root node \(`/`\) from the **Actions** drop\-down menu at the top\-right corner of the **Resources** panel\.

1. Choose the `GET` from the drop\-down list of HTTP verbs, and choose the check\-mark icon to start creating the method\.  
![\[Create a method for integration with Amazon S3\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/aws_proxy_apig_method_create.png)

1. In the **/ \- GET \- Setup** pane, choose **AWS Service** for **Integration type**\.

1. From the list, choose a region \(e\.g\., `us-west-2`\) for **AWS Region**\.

1. From **AWS Service**, choose **S3**\.

1. For **AWS Subdomain**, leave it blank\.

1. From **HTTP method**, choose **GET**\.

1. For **Action Type**, choose **Use path override**\. With path override, API Gateway forwards the client request to Amazon S3 as the corresponding [Amazon S3 REST API path\-style request](https://docs.aws.amazon.com/AmazonS3/latest/dev/RESTAPI.html), in which a Amazon S3 resource is expressed by the resource path of the `s3-host-name/bucket/key` pattern\. API Gateway sets the `s3-host-name` and passes the client specified `bucket` and `key` from the client to Amazon S3\.

1. \(Optional\) In **Path override** type **/**\.

1. Copy the previously created IAM role's ARN \(from the IAM console\) and paste it into **Execution role**\.

1. Leave any other settings as default\.

1. Choose **Save** to finish setting up this method\.

This setup integrates the frontend `GET https://your-api-host/stage/` request with the backend `GET https://your-s3-host/`\.

**Note**  
 After the initial setup, you can modify these settings in the **Integration Request** page of the method\. 

To control who can call this method of our API, we turn on the method authorization flag and set it to `AWS_IAM`\. 

**To enable IAM to control access to the GET / method**

1. From the **Method Execution**, choose **Method Request**\.

1. Choose the pencil icon next to **Authorization**

1. Choose `AWS_IAM` from the drop\-down list\.

1. Choose the check\-mark icon to save the setting\.  
![\[Declare method response types\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/aws_proxy_s3_setup_method_request_authorization.png)

 For our API to return successful responses and exceptions properly to the caller, let us declare the 200, 400 and 500 responses in **Method Response**\. We use the default mapping for 200 responses so that backend responses of the status code not declared here will be returned to the caller as 200 ones\. 

**To declare response types for the GET / method**

1. From the **Method Execution** pane, choose the **Method Response** box\. The API Gateway declares the 200 response by default\.

1. Choose **Add response**, enter **400** in the input text box, and choose the check\-mark to finish the declaration\.

1. Repeat the above step to declare the 500 response type\. The final setting is shown as follows:  
![\[Declare method response types\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/aws_proxy_s3_setup_method_response_types.png)

Because the successful integration response from Amazon S3 returns the bucket list as an XML payload and the default method response from API Gateway returns a JSON payload, we must map the backend Content\-Type header parameter value to the frontend counterpart\. Otherwise, the client will receive `application/json` for the content type when the response body is actually an XML string\. The following procedure shows how to set this up\. In addition, we also want to display to the client other header parameters, such as Date and Content\-Length\. 

**To set up response header mappings for the GET / method**

1.  In the API Gateway console, choose **Method Response**\. Add the **Content\-Type** header for the 200 response type\.   
![\[Declare method response headers\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/aws_proxy_s3_setup_method_response_headers.png)

1.  In **Integration Response**, for **Content\-Type**, type `integration.response.header.Content-Type` for the method response\.   
![\[Map integration response headers to method response headers\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/aws_proxy_s3_setup_integration_response_headers.png)

   With the above header mappings, API Gateway will translate the `Date` header from the backend to the `Timestamp` header for the client\. 

1. Still in **Integration Response**, choose **Add integration response**, type an appropriate regular expression in the **HTTP status regex** text box for a remaining method response status\. Repeat until all the method response status are covered\.  
![\[Set up integration response status codes\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/aws_proxy_s3_setup_integration_response_status.png)

As a good practice, let us test our API we have configured so far\.

**Test the GET method on the API root resource**

1. Go back to **Method Execution**, choose **Test** from the **Client** box\.

1. Choose **Test** in the **GET / \- Method Test** pane\. An example result is shown as follows\.   
![\[Test API root GET bucket result\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/aws_proxy_s3_test_root_get_result.png)

**Note**  
 To use the API Gateway console to test the API as an Amazon S3 proxy, make sure that the targeted S3 bucket is from a different region from the API's region\. Otherwise, you may get a 500 Internal Server Error response\. This limitation does not apply to any deployed API\. 

## Expose API methods to access an Amazon S3 bucket<a name="api-folder-operations-as-s3-bucket-actions"></a>

To work with an Amazon S3 bucket, we expose the GET, PUT, and DELETE methods on the /\{folder\} resource to list objects in a bucket, create a new bucket, and delete an existing bucket\. The instructions are similar to those described in [Expose an API method to list the caller's Amazon S3 buckets](#api-root-get-as-s3-get-service)\. In the following discussions, we outline the general tasks and highlight relevant differences\.

**To expose GET, PUT and DELETE methods on a folder resource**

1.  On the **/\{folder\}** node from the **Resources** tree, create the DELETE, GET and PUT methods, one at a time\. 

1. Set up the initial integration of each created method with its corresponding Amazon S3 endpoints\. The following screen shot illustrates this setting for the `PUT /{folder}` method\. For the `DELETE /{folder}` and `GET /{folder}` method, replace the `PUT` value of **HTTP method** by `DELETE` and `GET`, respectively\.  
![\[Set up PUT /{folder} method\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/aws_proxy_s3_create_methods_on_folder.png)

   Notice that we used the `{bucket}` path parameter in the Amazon S3 endpoint URLs to specify the bucket\. We will need to map the `{folder}` path parameter of the method requests to the `{bucket}` path parameter of the integration requests\.

1. To map `{folder}` to `{bucket}`:

   1. Choose **Method Execution** and then **Integration Request**\.

   1. Expand **URL Path Parameters** and choose **Add path**

   1. Type `bucket` in the **Name** column and `method.request.path.folder` in the **Mapped from** column\. Choose the check\-mark icon to save the mapping\.  
![\[Set up PUT /{folder} method\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/aws_proxy_s3_create_methods_on_folder_map_path_parameter.png)

1. In **Method Request**, add the `Content-Type` to the **HTTP Request Headers** section\.  
![\[Set up PUT /{folder} method\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/aws_proxy_s3_create_methods_on_folder_setup_request_header.png)

    This is mostly needed for testing, when using the API Gateway console, when you must specify **application/xml** for an XML payload\.

1. In **Integration Request**, set up the following header mappings, following the instructions described in [Expose an API method to list the caller's Amazon S3 buckets](#api-root-get-as-s3-get-service)\.  
![\[Set up header mappings for the PUT /{folder} method\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/aws_proxy_s3_create_methods_on_folder_header_mappings.png)

    The `x-amz-acl` header is for specifying access control on the folder \(or the corresponding Amazon S3 bucket\)\. For more information, see [Amazon S3 PUT Bucket Request](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUT.html#RESTBucketPUT-requests)\. 

1. To test the `PUT` method, choose **Test** in the **Client** box from **Method Execution**, and enter the following as input to the testing: 

   1. In **folder**, type a bucket name,

   1. For the **Content\-Type** header, type **application/xml**\.

   1. In **Request Body**, provide the bucket region as the location constraint, declared in an XML fragment as the request payload\. For example,

      ```
      <CreateBucketConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/"> 
         <LocationConstraint>{region}</LocationConstraint> 
      </CreateBucketConfiguration>
      ```  
![\[Test the PUT method to create an Amazon S3 bucket.\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/aws_proxy_s3_test_api_folder_put.png)

1.  Repeat the preceding steps to create and configure the GET and DELETE method on the API's **/\{folder\}** resource\. 

The above examples illustrate how to create a new bucket in the specified region, to view the list of objects in the bucket, and to delete the bucket\. Other Amazon S3 bucket operations allow you work with the metadata or properties of the bucket\. For example, you can set up your API to call the Amazon S3's [PUT /?notification](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUTnotification.html#RESTBucketPUTnotification-requests) action to set up notifications on the bucket, to call [PUT /?acl](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUTacl.html#RESTBucketPUTacl-requests) to set an access control list on the bucket, etc\. The API set up is similar, except for that you must append appropriate query parameters to the Amazon S3 endpoint URLs\. At run time, you must provide the appropriate XML payload to the method request\. The same can be said about supporting the other GET and DELETE operations on a Amazon S3 bucket\. For more information on possible Amazon S3 actions on a bucket, see [Amazon S3 Operations on Buckets](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketOps.html)\.

## Expose API methods to access an Amazon S3 object in a bucket<a name="api-items-in-folder-as-s3-objects-in-bucket"></a>

Amazon S3 supports GET, DELETE, HEAD, OPTIONS, POST and PUT actions to access and manage objects in a given bucket\. For the complete list of supported actions, see [Amazon S3 Operations on Objects](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectOps.html)\.

In this tutorial, we expose the [PUT Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPUT.html) operation, the [GET Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectGET.html) operation, [HEAD Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectHEAD.html) operation, and the [DELETE Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectDELETE.html) operation through the API methods of `PUT /{folder}/{item}`, `GET /{folder}/{item}`, `HEAD /{folder}/{item}` and `DELETE /{folder}/{item}`, respectively\.

The API setups for the PUT, GET and DELETE methods on `/{folder}/{item}` are the similar to those on `/{folder}`, as prescribed in [Expose API methods to access an Amazon S3 bucket](#api-folder-operations-as-s3-bucket-actions)\. One major difference is that the object\-related request path has an additional path parameter of `{item}` and this path parameter must be mapped to the integration request path parameter of `{object}`\. 

![\[Integrate the PUT method request on /{folder}/{item} resource with Amazon S3\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/aws_proxy_s3_setup_object_integration_request.png)

The same is true for the GET and DELETE methods\.

As an illustration, the following screen shot shows the output when testing the GET method on a `{folder}/{item}` resource using the API Gateway console\. The request correctly returns the plain text of \("Welcome to README\.txt"\) as the content of the specified file \(README\.txt\) in the given Amazon S3 bucket \(apig\-demo\)\. 

![\[Test API Folder/Item GET bucket result\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/aws_proxy_s3_test_folder_item_get_result.png)

 To download or upload binary files, which in API Gateway is considered any thing other than utf\-8 encoded JSON content, additional API settings are necessary\. This is outlined as follows: 

**To download or upload binary files from S3**

1.  Register the media types of the affected file to the API's binaryMediaTypes\. You can do this in the console: 

   1. Choose **Settings** for the API\.

   1. Under **Binary Media Types**, choose **Add Binary Media Type**\.

   1. Enter the required media type, for example, `image/png`\.

   1. Choose **Save Changes** to save the setting\.

1. Add the `Content-Type` \(for upload\) and/or `Accept` \(for download\) header to the method request to require the client to specify the required binary media type and map them to the integration request\.

1. Set **Content Handling** to `Passthrough` in the integration request \(for upload\) and in a integration response \(for download\)\. Make sure that no mapping template is defined for the affected content type\. For more information, see [Integration Passthrough Behaviors](integration-passthrough-behaviors.md) and [Select VTL Mapping Templates](request-response-data-mappings.md#transforming-request-response-body)\.

The payload size limit is 10 MB\. See [API Gateway quotas for configuring and running a REST API](limits.md#api-gateway-execution-service-limits-table)\.

Make sure that files on Amazon S3 have the correct content types added as the files' metadata\. For streamable media content, `Content-Disposition:inline` may also need to be added to the metadata\.

For more information about the binary support in API Gateway, see [Content type conversions in API Gateway](api-gateway-payload-encodings-workflow.md)\.

## Call the API using a REST API client<a name="api-as-s3-proxy-test-using-postman"></a>

To provide an end\-to\-end tutorial, we now show how to call the API using [Postman](https://www.postman.com/), which supports the AWS IAM authorization\.<a name="api-as-s3-proxy-test-using-postman-steps"></a>

**To call our Amazon S3 proxy API using Postman**

1. Deploy or redeploy the API\. Make a note of the base URL of the API that is displayed next to **Invoke URL** at the top of the **Stage Editor**\.

1. Launch Postman\.

1. Choose **Authorization** and then choose `AWS Signature`\. Type your IAM user's Access Key ID and Secret Access Key into the **AccessKey** and **SecretKey**input fields, respectively\. Type the AWS region to which your API is deployed in the **AWS Region** text box\. Type `execute-api` in the **Service Name** input field\.

   You can create a pair of the keys from the **Security Credentials** tab from your IAM user account in the IAM Management Console\.

1. To add a bucket named `apig-demo-5` to your Amazon S3 account in the `{region}` region:
**Note**  
Be sure that the bucket name must be globally unique\.

   1. Choose **PUT** from the drop\-down method list and type the method URL \(`https://api-id.execute-api.aws-region.amazonaws.com/stage/folder-name`

   1. Set the `Content-Type` header value as `application/xml`\. You may need to delete any existing headers before setting the content type\.

   1. Choose **Body** menu item and type the following XML fragment as the request body:

      ```
      <CreateBucketConfiguration> 
        <LocationConstraint>{region}</LocationConstraint> 
      </CreateBucketConfiguration>
      ```

   1. Choose **Send** to submit the request\. If successful, you should receive a `200 OK` response with an empty payload\. 

1. To add a text file to a bucket, follow the instructions above\. If you specify a bucket name of **apig\-demo\-5** for `{folder}` and a file name of **Readme\.txt** for `{item}` in the URL and provide a text string of **Hello, World\!** as the file contents \(thereby making it the request payload\), the request becomes

   ```
   PUT /S3/apig-demo-5/Readme.txt HTTP/1.1
   Host: 9gn28ca086.execute-api.{region}.amazonaws.com
   Content-Type: application/xml
   X-Amz-Date: 20161015T062647Z
   Authorization: AWS4-HMAC-SHA256 Credential=access-key-id/20161015/{region}/execute-api/aws4_request, SignedHeaders=content-length;content-type;host;x-amz-date, Signature=ccadb877bdb0d395ca38cc47e18a0d76bb5eaf17007d11e40bf6fb63d28c705b
   Cache-Control: no-cache
   Postman-Token: 6135d315-9cc4-8af8-1757-90871d00847e
   
   Hello, World!
   ```

   If everything goes well, you should receive a `200 OK` response with an empty payload\.

1. To get the content of the `Readme.txt` file we just added to the `apig-demo-5` bucket, do a GET request like the following one:

   ```
   GET /S3/apig-demo-5/Readme.txt HTTP/1.1
   Host: 9gn28ca086.execute-api.{region}.amazonaws.com
   Content-Type: application/xml
   X-Amz-Date: 20161015T063759Z
   Authorization: AWS4-HMAC-SHA256 Credential=access-key-id/20161015/{region}/execute-api/aws4_request, SignedHeaders=content-type;host;x-amz-date, Signature=ba09b72b585acf0e578e6ad02555c00e24b420b59025bc7bb8d3f7aed1471339
   Cache-Control: no-cache
   Postman-Token: d60fcb59-d335-52f7-0025-5bd96928098a
   ```

   If successful, you should receive a `200 OK` response with the `Hello, World!` text string as the payload\.

1. To list items in the `apig-demo-5` bucket, submit the following request:

   ```
   GET /S3/apig-demo-5 HTTP/1.1
   Host: 9gn28ca086.execute-api.{region}.amazonaws.com
   Content-Type: application/xml
   X-Amz-Date: 20161015T064324Z
   Authorization: AWS4-HMAC-SHA256 Credential=access-key-id/20161015/{region}/execute-api/aws4_request, SignedHeaders=content-type;host;x-amz-date, Signature=4ac9bd4574a14e01568134fd16814534d9951649d3a22b3b0db9f1f5cd4dd0ac
   Cache-Control: no-cache
   Postman-Token: 9c43020a-966f-61e1-81af-4c49ad8d1392
   ```

   If successful, you should receive a `200 OK` response with an XML payload showing a single item in the specified bucket, unless you added more files to the bucket before submitting this request\.

   ```
   <?xml version="1.0" encoding="UTF-8"?>
   <ListBucketResult xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
       <Name>apig-demo-5</Name>
       <Prefix></Prefix>
       <Marker></Marker>
       <MaxKeys>1000</MaxKeys>
       <IsTruncated>false</IsTruncated>
       <Contents>
           <Key>Readme.txt</Key>
           <LastModified>2016-10-15T06:26:48.000Z</LastModified>
           <ETag>"65a8e27d8879283831b664bd8b7f0ad4"</ETag>
           <Size>13</Size>
           <Owner>
               <ID>06e4b09e9d...603addd12ee</ID>
               <DisplayName>user-name</DisplayName>
           </Owner>
           <StorageClass>STANDARD</StorageClass>
       </Contents>
   </ListBucketResult>
   ```

**Note**  
To upload or download an image, you need to set content handling to CONVERT\_TO\_BINARY\.