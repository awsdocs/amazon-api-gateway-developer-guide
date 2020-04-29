# Tutorial: Create a REST API as an Amazon Kinesis proxy in API Gateway<a name="integrating-api-with-aws-services-kinesis"></a>

This page describes how to create and configure a REST API with an integration of the `AWS` type to access Kinesis\. 

**Note**  
 To integrate your API Gateway API with Kinesis, you must choose a region where both the API Gateway and Kinesis services are available\. For region availability, see [Service Endpoints and Quotas](https://docs.aws.amazon.com/general/latest/gr/aws-service-information.html)\.

 For the purpose of illustration, we create an example API to enable a client to do the following: 

1. List the user's available streams in Kinesis 

1. Create, describe, or delete a specified stream

1. Read data records from or write data records into the specified stream

 To accomplish the preceding tasks, the API exposes methods on various resources to invoke the following, respectively: 

1. The `ListStreams` action in Kinesis 

1. The `CreateStream`, `DescribeStream`, or `DeleteStream` action

1. The `GetRecords` or `PutRecords` \(including `PutRecord`\) action in Kinesis

 Specifically, we build the API as follows: 
+  Expose an HTTP GET method on the API's `/streams` resource and integrate the method with the [ListStreams](https://docs.aws.amazon.com/kinesis/latest/APIReference/API_ListStreams.html) action in Kinesis to list the streams in the caller's account\. 
+  Expose an HTTP POST method on the API's `/streams/{stream-name}` resource and integrate the method with the [CreateStream](https://docs.aws.amazon.com/kinesis/latest/APIReference/API_CreateStream.html) action in Kinesis to create a named stream in the caller's account\. 
+  Expose an HTTP GET method on the API's `/streams/{stream-name}` resource and integrate the method with the [DescribeStream](https://docs.aws.amazon.com/kinesis/latest/APIReference/API_DescribeStream.html) action in Kinesis to describe a named stream in the caller's account\. 
+  Expose an HTTP DELETE method on the API's `/streams/{stream-name}` resource and integrate the method with the [DeleteStream](https://docs.aws.amazon.com/kinesis/latest/APIReference/API_DeleteStream.html) action in Kinesis to delete a stream in the caller's account\. 
+  Expose an HTTP PUT method on the API's `/streams/{stream-name}/record` resource and integrate the method with the [PutRecord](https://docs.aws.amazon.com/kinesis/latest/APIReference/API_PutRecord.html) action in Kinesis\. This enables the client to add a single data record to the named stream\. 
+  Expose an HTTP PUT method on the API's `/streams/{stream-name}/records` resource and integrate the method with the [PutRecords](https://docs.aws.amazon.com/kinesis/latest/APIReference/API_PutRecords.html) action in Kinesis\. This enables the client to add a list of data records to the named stream\. 
+  Expose an HTTP GET method on the API's `/streams/{stream-name}/records` resource and integrate the method with the [GetRecords](https://docs.aws.amazon.com/kinesis/latest/APIReference/API_GetRecords.html) action in Kinesis\. This enables the client to list data records in the named stream, with a specified shard iterator\. A shard iterator specifies the shard position from which to start reading data records sequentially\.
+  Expose an HTTP GET method on the API's `/streams/{stream-name}/sharditerator` resource and integrate the method with the [GetShardIterator](https://docs.aws.amazon.com/kinesis/latest/APIReference/API_GetShardIterator.html) action in Kinesis\. This helper method must be supplied to the `ListStreams` action in Kinesis\. 

 You can apply the instructions presented here to other Kinesis actions\. For the complete list of the Kinesis actions, see [Amazon Kinesis API Reference](https://docs.aws.amazon.com/kinesis/latest/APIReference/Welcome.html)\. 

 Instead of using the API Gateway console to create the sample API, you can import the sample API into API Gateway using the API Gateway [Import API](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/restapi-import/)\. For information on how to use the Import API, see [Configuring a REST API using OpenAPI](api-gateway-import-api.md)\. 

If you do not have an AWS account, complete the following steps to create one\.

**To sign up for an AWS account**

1. Open [https://portal\.aws\.amazon\.com/billing/signup](https://portal.aws.amazon.com/billing/signup)\.

1. Follow the online instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a verification code on the phone keypad\.

## Create an IAM role and policy for the API to access Kinesis<a name="integrate-with-kinesis-create-iam-role-and-policy"></a>

 To allow the API to invoke Kinesis actions, you must have appropriate IAM policies attached to an IAM role\. This section explains how to verify and to create, if necessary, the required IAM role and policies\. 

 To enable read\-only access to Kinesis, you can use the AmazonKinesisReadOnlyAccess policy that allows the `Get*`, `List*`, and `Describe*` actions in Kinesis to be invoked\. 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "kinesis:Get*",
                "kinesis:List*",
                "kinesis:Describe*"
            ],
            "Resource": "*"
        }
    ]
}
```

 This policy is available as an IAM\-provisioned managed policy and its ARN is `arn:aws:iam::aws:policy/AmazonKinesisReadOnlyAccess`\. 

 To enable read\-write actions in Kinesis, you can use the AmazonKinesisFullAccess policy\. 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "kinesis:*",
            "Resource": "*"
        }
    ]
}
```

 This policy is also available as an IAM\-provisioned managed policy\. Its ARN is `arn:aws:iam::aws:policy/AmazonKinesisFullAccess`\. 

 After you decide which IAM policy to use, attach it to a new or existing IAM role\. Make sure that the API Gateway control service \(`apigateway.amazonaws.com`\) is a trusted entity of the role and is allowed to assume the execution role \(`sts:AssumeRole`\)\. 

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

If you create the execution role in the IAM console and choose the **Amazon API Gateway** role type, this trust policy is automatically attached\. 

 Note the ARN of the execution role\. You will need it when creating an API method and setting up its integration request\. 

## Start to create an API as a Kinesis proxy<a name="api-gateway-create-api-as-kinesis-proxy"></a>

Use the following steps to create the API in the API Gateway console\.

**To create an API as an AWS service proxy for Kinesis**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. If this is your first time using API Gateway, you see a page that introduces you to the features of the service\. Under **REST API**, choose **Build**\. When the **Create Example API** popup appears, choose **OK**\.

   If this is not your first time using API Gateway, choose **Create API**\. Under **REST API**, choose **Build**\.

1. Choose **New API**\. 

1. In **API name**, enter **KinesisProxy**\. Leave the default values in the other fields\. 

1. Enter a description in **Description**, if you like\. 

1. Choose **Create API**\. 

 After the API is created, the API Gateway console displays the **Resources** page, which contains only the API's root \(`/`\) resource\. 

## List streams in Kinesis<a name="api-gateway-list-kinesis-streams"></a>

 Kinesis supports the `ListStreams` action with the following REST API call: 

```
POST /?Action=ListStreams HTTP/1.1
Host: kinesis.<region>.<domain>
Content-Length: <PayloadSizeBytes>
User-Agent: <UserAgentString>
Content-Type: application/x-amz-json-1.1
Authorization: <AuthParams>
X-Amz-Date: <Date>
        
{
   ...
}
```

In the above REST API request, the action is specified in the `Action` query parameter\. Alternatively, you can specify the action in a `X-Amz-Target` header, instead:

```
POST / HTTP/1.1
Host: kinesis.<region>.<domain>
Content-Length: <PayloadSizeBytes>
User-Agent: <UserAgentString>
Content-Type: application/x-amz-json-1.1
Authorization: <AuthParams>
X-Amz-Date: <Date>
X-Amz-Target: Kinesis_20131202.ListStreams        
{
   ...
}
```

In this tutorial, we use the query parameter to specify action\.

To expose a Kinesis action in the API, add a `/streams` resource to the API's root\. Then set a `GET` method on the resource and integrate the method with the `ListStreams` action of Kinesis\. 

The following procedure describes how to list Kinesis streams by using the API Gateway console\. 

**To list Kinesis streams by using the API Gateway console**

1.  Select the API root resource\. In **Actions**, choose **Create Resource**\. 

   In **Resource Name**, type `Streams`, leave **Resource Path** and other fields as the default, and choose **Create Resource**\. 

1.  Choose the `/Streams` resource\. From **Actions**, choose **Create Method**, choose **GET** from the list, and then choose the check mark icon to finish creating the method\. 
**Note**  
The HTTP verb for a method invoked by a client may differ from the HTTP verb for an integration required by the backend\. We chose `GET` here, because listing streams is intuitively a READ operation\. 

1.  In the method's **Setup** pane, choose **AWS Service**\. 

   1. For **AWS Region**, choose a region \(e\.g\., **us\-east\-1**\)\.

   1. For **AWS Service**, choose **Kinesis**\.

   1. Leave **AWS Subdomain** blank\.

   1. For **HTTP method**, choose **POST**\.
**Note**  
We chose `POST` here because Kinesis requires that the `ListStreams` action be invoked with it\. 

   1. For **Action Type**, choose **Use action name**\.

   1. For **Action**, type **ListStreams**\.

   1. For **Execution role**, type the ARN for your execution role\.

   1. Leave the default of **Passthrough** for **Content Handling**\.

   1. Choose **Save** to finish the initial setup of the method\.  
![\[Set up the Streams:GET method for Kinesis ListStreams action.\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-kinesis-proxy-setup-streams-get-method.png)

1.  Still in the **Integration Request** pane, expand the **HTTP Headers** section:

   1. Choose **Add header**\.

   1. In the **Name** column, type `Content-Type`\.

   1. In the **Mapped from** column, type `'application/x-amz-json-1.1'`\.

   1. Choose the check mark icon to save the setting\.

    We used a request parameter mapping to set the `Content-Type` header to the static value of `'application/x-amz-json-1.1'` to inform Kinesis that the input is of a specific version of JSON\. 

1. Expand the **Body Mapping Templates** section: 

   1. Choose **Add mapping template**\.

   1. For **Content\-Type**, type `application/json`\.

   1. Choose the check mark icon to save the **Content\-Type** setting\. Choose **Yes, secure this integration** in **Change passthrough behavior**\.

   1. Type **\{\}** in the template editor\.

   1. Choose the **Save** button to save the mapping template\.

    The [ListStreams](https://docs.aws.amazon.com/kinesis/latest/APIReference/API_ListStreams.html#API_ListStreams_RequestSyntax) request takes a payload of the following JSON format: 

   ```
   {
       "ExclusiveStartStreamName": "string",
       "Limit": number
   }
   ```

   However, the properties are optional\. To use the default values, we opted for an empty JSON payload here\.  
![\[Set up data mapping for the Streams:GET method for the Kinesis ListStreams action.\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-kinesis-proxy-setup-streams-get-method-data-mapping.png)

1. Test the GET method on the Streams resource to invoke the `ListStreams` action in Kinesis:

    From the API Gateway console, select the **/streams/GET** entry from the **Resources** pane, choose the **Test** invocation option, and then choose **Test**\. 

    If you already created two streams named "myStream" and "yourStream" in Kinesis, the successful test returns a 200 OK response containing the following payload: 

   ```
   {
        "HasMoreStreams": false,
        "StreamNames": [
            "myStream",
            "yourStream"
        ]
   }
   ```

## Create, describe, and delete a stream in Kinesis<a name="api-gateway-create-describe-delete-stream"></a>

 Creating, describing, and deleting a stream in Kinesis involves making the following Kinesis REST API requests, respectively: 

```
POST /?Action=CreateStream HTTP/1.1
Host: kinesis.region.domain
...
Content-Type: application/x-amz-json-1.1
Content-Length: PayloadSizeBytes

{
    "ShardCount": number,
    "StreamName": "string"
}
```

```
POST /?Action=DescribeStream HTTP/1.1
Host: kinesis.region.domain
...
Content-Type: application/x-amz-json-1.1
Content-Length: PayloadSizeBytes

{
    "ExclusiveStartShardId": "string",
    "Limit": number,
    "StreamName": "string"
}
```

```
POST /?Action=DeleteStream HTTP/1.1
Host: kinesis.region.domain
...
Content-Type: application/x-amz-json-1.1
Content-Length: PayloadSizeBytes

{
    "StreamName":"string"
}
```

 We can build the API to accept the required input as a JSON payload of the method request and pass the payload through to the integration request\. However, to provide more examples of data mapping between method and integration requests, and method and integration responses, we create our API somewhat differently\. 

 We expose the `GET`, `POST`, and `Delete` HTTP methods on a to\-be\-named `Stream` resource\. We use the `{stream-name}` path variable as the placeholder of the stream resource and integrate these API methods with the Kinesis' `DescribeStream`, `CreateStream`, and `DeleteStream` actions, respectively\. We require that the client pass other input data as headers, query parameters, or the payload of a method request\. We provide mapping templates to transform the data to the required integration request payload\. 

**To configure and test the GET method on a stream resource**

1.  Create a child resource with the `{stream-name}` path variable under the previously created `/streams` resource\.   
![\[Create {stream-name} resource\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-kinesis-proxy-setup-streams-stream-resource.png)

1.  Add the `POST`, `GET`, and `DELETE` HTTP verbs to this resource\.

    After the methods are created on the resource, the structure of the API looks like the following:   
![\[Create POST, GET, and DELETE methods on a stream resource\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-kinesis-proxy-setup-streams-stream-methods.png)

1. Set up the `GET /streams/{stream-name}` method to call the `POST /?Action=DescribeStream` action in Kinesis, as shown in the following\.   
![\[Set up the GET-on-stream method to invoke the DescribeStream action in Kinesis\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-kinesis-proxy-setup-streams-stream-get-method.png)

1. Add the following `Content-Type` header mapping to the integration request:

   ```
   Content-Type: 'x-amz-json-1.1'
   ```

   The task follows the same procedure to set up the request parameter mapping for the `GET /streams` method\.

1. Add the following body mapping template to map data from the `GET /streams/{stream-name}` method request to the `POST /?Action=DescribeStream` integration request:

   ```
   {
       "StreamName": "$input.params('stream-name')"
   }
   ```

   This mapping template generates the required integration request payload for the `DescribeStream` action of Kinesis from the method request's `stream-name` path parameter value\.

1. Test the `GET /stream/{stream-name}` method to invoke the `DescribeStream` action in Kinesis:

   From the API Gateway console, select **/streams/\{stream\-name\}/GET** in the **Resources** pane, choose **Test** to start testing, type the name of an existing Kinesis stream in the **Path** field for `stream-name`, and choose **Test**\. If the test is successful, a 200 OK response is returned with a payload similar to the following: 

   ```
   {
     "StreamDescription": {
       "HasMoreShards": false,
       "RetentionPeriodHours": 24,
       "Shards": [
         {
           "HashKeyRange": {
             "EndingHashKey": "68056473384187692692674921486353642290",
             "StartingHashKey": "0"
           },
           "SequenceNumberRange": {
             "StartingSequenceNumber": "49559266461454070523309915164834022007924120923395850242"
           },
           "ShardId": "shardId-000000000000"
         },
         ...
         {
           "HashKeyRange": {
             "EndingHashKey": "340282366920938463463374607431768211455",
             "StartingHashKey": "272225893536750770770699685945414569164"
           },
           "SequenceNumberRange": {
             "StartingSequenceNumber": "49559266461543273504104037657400164881014714369419771970"
           },
           "ShardId": "shardId-000000000004"
         }
       ],
       "StreamARN": "arn:aws:kinesis:us-east-1:12345678901:stream/myStream",
       "StreamName": "myStream",
       "StreamStatus": "ACTIVE"
     }
   }
   ```

    After you deploy the API, you can make a REST request against this API method: 

   ```
   GET https://your-api-id.execute-api.region.amazonaws.com/stage/streams/myStream HTTP/1.1
   Host: your-api-id.execute-api.region.amazonaws.com
   Content-Type: application/json
   Authorization: ...
   X-Amz-Date: 20160323T194451Z
   ```

**To configure and test the POST method on a stream resource**

1. Set up the `POST /streams/{stream-name}` method to call `POST /?Action=CreateStream` action in Kinesis\. The task follows the same procedure to set up the `GET /streams/{stream-name}` method provided that you replace the `DescribeStream` action by `CreateStream`\. 

1. Add the following `Content-Type` header mapping to the integration request:

   ```
   Content-Type: 'x-amz-json-1.1'
   ```

   The task follows the same procedure to set up the request parameter mapping for the `GET /streams` method\.

1.  Add the following body mapping template to map data from the `POST /streams/{stream-name}` method request to the `POST /?Action=CreateStream` integration request: 

   ```
   {
       "ShardCount": #if($input.path('$.ShardCount') == '') 5 #else $input.path('$.ShardCount') #end,
       "StreamName": "$input.params('stream-name')"
   }
   ```

    In the preceding mapping template, we set `ShardCount` to a fixed value of 5 if the client does not specify a value in the method request payload\. 

1. Test the `POST /streams/{stream-name}` method to create a named stream in Kinesis:

   From the API Gateway console, select **/streams/\{stream\-name\}/POST** in the **Resources** pane, choose **Test** to start testing, type the name of an existing Kinesis stream in **Path** for `stream-name`, and choose **Test**\. If the test is successful, a 200 OK response is returned with no data\. 

    After you deploy the API, you can also make a REST API request against the POST method on a Stream resource to invoke the `CreateStream` action in Kinesis: 

   ```
   POST https://your-api-id.execute-api.region.amazonaws.com/stage/streams/yourStream HTTP/1.1
   Host: your-api-id.execute-api.region.amazonaws.com
   Content-Type: application/json
   Authorization: ...
   X-Amz-Date: 20160323T194451Z
   
   { 
       "ShardCount": 5
   }
   ```

**Configure and test the DELETE method on a stream resource**

1. Set up the `DELETE /streams/{stream-name}` method to integrate with the `POST /?Action=DeleteStream` action in Kinesis\. The task follows the same procedure to set up the `GET /streams/{stream-name}` method provided that you replace the `DescribeStream` action by `DeleteStream`\. 

1. Add the following `Content-Type` header mapping to the integration request:

   ```
   Content-Type: 'x-amz-json-1.1'
   ```

   The task follows the same procedure to set up the request parameter mapping for the `GET /streams` method\.

1.  Add the following body mapping template to map data from the `DELETE /streams/{stream-name}` method request to the corresponding integration request of `POST /?Action=DeleteStream` : 

   ```
   {
       "StreamName": "$input.params('stream-name')"
   }
   ```

    This mapping template generates the required input for the `DELETE /streams/{stream-name}` action from the client\-supplied URL path name of `stream-name`\. 

1. Test the DELETE method to delete a named stream in Kinesis:

   From the API Gateway console, select the **/streams/\{stream\-name\}/DELETE** method node in the **Resources** pane, choose **Test** to start testing, type the name of an existing Kinesis stream in **Path** for `stream-name`, and choose **Test**\. If the test is successful, a 200 OK response is returned with no data\. 

    After you deploy the API, you can also make the following REST API request against the DELETE method on the Stream resource to call the `DeleteStream` action in Kinesis: 

   ```
   DELETE https://your-api-id.execute-api.region.amazonaws.com/stage/streams/yourStream HTTP/1.1
   Host: your-api-id.execute-api.region.amazonaws.com
   Content-Type: application/json
   Authorization: ...
   X-Amz-Date: 20160323T194451Z
   
   {}
   ```

## Get records from and add records to a stream in Kinesis<a name="api-gateway-get-and-add-records-to-stream"></a>

 After you create a stream in Kinesis, you can add data records to the stream and read the data from the stream\. Adding data records involves calling the [PutRecords](https://docs.aws.amazon.com/kinesis/latest/APIReference/API_PutRecords.html#API_PutRecords_Examples) or [PutRecord](https://docs.aws.amazon.com/kinesis/latest/APIReference/API_PutRecord.html#API_PutRecord_Examples) action in Kinesis\. The former adds multiple records whereas the latter adds a single record to the stream\. 

```
POST /?Action=PutRecords HTTP/1.1
Host: kinesis.region.domain
Authorization: AWS4-HMAC-SHA256 Credential=..., ...
...
Content-Type: application/x-amz-json-1.1
Content-Length: PayloadSizeBytes

{
    "Records": [
        {
            "Data": blob,
            "ExplicitHashKey": "string",
            "PartitionKey": "string"
        }
    ],
    "StreamName": "string"
}
```

or

```
POST /?Action=PutRecord HTTP/1.1
Host: kinesis.region.domain
Authorization: AWS4-HMAC-SHA256 Credential=..., ...
...
Content-Type: application/x-amz-json-1.1
Content-Length: PayloadSizeBytes

{
    "Data": blob,
    "ExplicitHashKey": "string",
    "PartitionKey": "string",
    "SequenceNumberForOrdering": "string",
    "StreamName": "string"
}
```

 Here, `StreamName` identifies the target stream to add records\. `StreamName`, `Data`, and `PartitionKey` are required input data\. In our example, we use the default values for all of the optional input data and will not explicitly specify values for them in the input to the method request\. 

 Reading data in Kinesis amounts to calling the [GetRecords](https://docs.aws.amazon.com/kinesis/latest/APIReference/API_GetRecords.html#API_GetRecords_Examples) action: 

```
POST /?Action=GetRecords HTTP/1.1
Host: kinesis.region.domain
Authorization: AWS4-HMAC-SHA256 Credential=..., ...
...
Content-Type: application/x-amz-json-1.1
Content-Length: PayloadSizeBytes

{
    "ShardIterator": "string",
    "Limit": number
}
```

Here, the source stream from which we are getting records is specified in the required `ShardIterator` value, as is shown in the following Kinesis action to obtain a shard iterator:

```
POST /?Action=GetShardIterator HTTP/1.1
Host: kinesis.region.domain
Authorization: AWS4-HMAC-SHA256 Credential=..., ...
...
Content-Type: application/x-amz-json-1.1
Content-Length: PayloadSizeBytes
                
{
    "ShardId": "string",
    "ShardIteratorType": "string",
    "StartingSequenceNumber": "string",
    "StreamName": "string"
}
```

 For the `GetRecords` and `PutRecords` actions, we expose the `GET` and `PUT` methods, respectively, on a `/records` resource that is appended to a named stream resource \(`/{stream-name}`\)\. Similarly, we expose the `PutRecord` action as a `PUT` method on a `/record` resource\. 

 Because the `GetRecords` action takes as input a `ShardIterator` value, which is obtained by calling the `GetShardIterator` helper action, we expose a `GET` helper method on a `ShardIterator` resource \(`/sharditerator`\)\. 

The following figure shows the API structure of resources after the methods are created:

![\[Create Records:GET|PUT|PUT|GET method for the API.\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-kinesis-proxy-setup-streams-stream-records-methods.png)

 The following four procedures describe how to set up each of the methods, how to map data from the method requests to the integration requests, and how to test the methods\. 

**To set up and test the `PUT /streams/{stream-name}/record` method to invoke `PutRecord` in Kinesis:**

1. Set up the PUT method, as shown in the following:  
![\[Set up the PUT method to call the PutRecord action in Kinesis.\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-kinesis-proxy-setup-streams-stream-record-put-method.png)

1. Add the following request parameter mapping to set the `Content-Type` header to an AWS\-compliant version of JSON in the integration request:

   ```
   Content-Type: 'x-amz-json-1.1'
   ```

   The task follows the same procedure to set up the request parameter mapping for the `GET /streams` method\.

1.  Add the following body mapping template to map data from the `PUT /streams/{stream-name}/record` method request to the corresponding integration request of `POST /?Action=PutRecord` : 

   ```
   {
       "StreamName": "$input.params('stream-name')",
       "Data": "$util.base64Encode($input.json('$.Data'))",
       "PartitionKey": "$input.path('$.PartitionKey')"
   }
   ```

    This mapping template assumes that the method request payload is of the following format: 

   ```
   {
      "Data": "some data",
      "PartitionKey": "some key"
   }
   ```

   This data can be modeled by the following JSON schema:

   ```
   {
     "$schema": "http://json-schema.org/draft-04/schema#",
     "title": "PutRecord proxy single-record payload",
     "type": "object",
     "properties": {
         "Data": { "type": "string" },
         "PartitionKey": { "type": "string" }
     }
   }
   ```

    You can create a model to include this schema and use the model to facilitate generating the mapping template\. However, you can generate a mapping template without using any model\. 

1.  To test the `PUT /streams/{stream-name}/record` method, set the `stream-name` path variable to the name of an existing stream, supply a payload of the required format, and then submit the method request\. The successful result is a `200 OK `response with a payload of the following format: 

   ```
   {
     "SequenceNumber": "49559409944537880850133345460169886593573102115167928386",
     "ShardId": "shardId-000000000004"
   }
   ```

**To set up and test the `PUT /streams/{stream-name}/records` method to invoke `PutRecords` in Kinesis**

1. Set up the `PUT /streams/{stream-name}/records` method, as shown in the following:  
![\[Set up the PUT /streams/{stream-name}/records method for the Kinesis PutRecords action.\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-kinesis-proxy-setup-streams-stream-records-put-method.png)

1. Add the following request parameter mapping to set the `Content-Type` header to an AWS\-compliant version of JSON in the integration request:

   ```
   Content-Type: 'x-amz-json-1.1'
   ```

   The task follows the same procedure to set up the request parameter mapping for the `GET /streams` method\.

1.  Add the following body mapping template to map data from the `PUT /streams/{stream-name}/records` method request to the corresponding integration request of `POST /?Action=PutRecords` : 

   ```
   {
       "StreamName": "$input.params('stream-name')",
       "Records": [
          #foreach($elem in $input.path('$.records'))
             {
               "Data": "$util.base64Encode($elem.data)",
               "PartitionKey": "$elem.partition-key"
             }#if($foreach.hasNext),#end
           #end
       ]
   }
   ```

   This mapping template assumes that the method request payload can be modelled by the following JSON schema:

   ```
   {
     "$schema": "http://json-schema.org/draft-04/schema#",
     "title": "PutRecords proxy payload data",
     "type": "object",
     "properties": {
       "records": {
         "type": "array",
         "items": {
           "type": "object",
           "properties": {
             "data": { "type": "string" },
             "partition-key": { "type": "string" }
           }
         }
       }
     }
   }
   ```

    You can create a model to include this schema and use the model to facilitate generating the mapping template\. However, you can generate a mapping template without using any model\. 

   In this tutorial, we used two slightly different payload formats to illustrate that an API developer can choose to expose the backend data format to the client or hide it from the client\. One format is for the `PUT /streams/{stream-name}/records` method \(above\)\. Another format is used for the `PUT /streams/{stream-name}/record` method \(in the previous procedure\)\. In production environment, you should keep both formats consistent\. 

1.  To test the `PUT /streams/{stream-name}/records` method, set the `stream-name` path variable to an existing stream, supply the following payload, and submit the method request\. 

   ```
   {
       "records": [
           {
               "data": "some data",
               "partition-key": "some key"
           },
           {
               "data": "some other data",
               "partition-key": "some key"
           }
       ]
   }
   ```

   The successful result is a 200 OK response with a payload similar to the following output: 

   ```
   {
     "FailedRecordCount": 0,
     "Records": [
       {
         "SequenceNumber": "49559409944537880850133345460167468741933742152373764162",
         "ShardId": "shardId-000000000004"
       },
       {
         "SequenceNumber": "49559409944537880850133345460168677667753356781548470338",
         "ShardId": "shardId-000000000004"
       }
     ]
   }
   ```

**To set up and test the `GET /streams/{stream-name}/sharditerator` method invoke `GetShardIterator` in Kinesis**

The `GET /streams/{stream-name}/sharditerator` method is a helper method to acquire a required shard iterator before calling the `GET /streams/{stream-name}/records` method\.

1. Set up integration for the `GET /streams/{stream-name}/sharditerator` method, as shown in the following:  
![\[Set up the GET /streams/{stream-name}/sharditerator method.\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-kinesis-proxy-setup-streams-stream-sharditerator-get-method.png)

1.  The `GetShardIterator` action requires an input of a ShardId value\. To pass a client\-supplied `ShardId` value, we add a `shard-id` query parameter to the method request, as shown in the following:   
![\[Add the shard-id query parameter to the GET-on-ShardIterator method request.\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-kinesis-proxy-setup-streams-stream-sharditerator-get-method-shardid-query-string.png)

    In the following body\-mapping template, we set the `shard-id` query parameter value to the `ShardId` property value of the JSON payload as the input to the `GetShardIterator` action in Kinesis\. 

1. Configure the body mapping template to generate the required input \(`ShardId` and `StreamName`\) to the `GetShardIterator` action from the `shard-id` and `stream-name` parameters of the method request\. In addition, the mapping template also sets `ShardIteratorType` to `TRIM_HORIZON` as a default\.

   ```
   {
       "ShardId": "$input.params('shard-id')",
       "ShardIteratorType": "TRIM_HORIZON",
       "StreamName": "$input.params('stream-name')"
   }
   ```

1.  Using the **Test** option in the API Gateway console, enter an existing stream name as the `stream-name` **Path** variable value, set the `shard-id` **Query string** to an existing `ShardId` value \(e\.g\., `shard-000000000004`\), and choose **Test**\. 

    The successful response payload is similar to the following output: 

   ```
   {
     "ShardIterator": "AAAAAAAAAAFYVN3VlFy..."
   }
   ```

   Make note of the `ShardIterator` value\. You need it to get records from a stream\.

**To configure and test the `GET /streams/{stream-name}/records` method to invoke the `GetRecords` action in Kinesis**

1. Set up the `GET /streams/{stream-name}/records` method, as shown in the following:  
![\[Set up the GET /streams/{stream-name}/records method.\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-kinesis-proxy-setup-streams-stream-records-get-method.png)

1.  The `GetRecords` action requires an input of a `ShardIterator` value\. To pass a client\-supplied `ShardIterator` value, we add a `Shard-Iterator` header parameter to the method request, as shown in the following:   
![\[Add the Shard-Iterator header parameter to the GET-on-records method request.\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-gateway-kinesis-proxy-setup-streams-stream-records-get-method-shard-iterator-header.png)

1.  Set up the following mapping template to map the `Shard-Iterator` header parameter value to the `ShardIterator` property value of the JSON payload for the `GetRecords` action in Kinesis\. 

   ```
   {
       "ShardIterator": "$input.params('Shard-Iterator')"
   }
   ```

1.  Using the **Test** option in the API Gateway console, type an existing stream name as the `stream-name` **Path** variable value, set the `Shard-Iterator` **Header** to the `ShardIterator` value obtained from the test run of the `GET /streams/{stream-name}/sharditerator` method \(above\), and choose **Test**\. 

    The successful response payload is similar to the following output: 

   ```
   {
     "MillisBehindLatest": 0,
     "NextShardIterator": "AAAAAAAAAAF...",
     "Records": [ ... ]
   }
   ```