# Basic tasks of an API integration request<a name="integration-request-basic-setup"></a>

 An integration request is an HTTP request that API Gateway submits to the backend, passing along the client\-submitted request data, and transforming the data, if necessary\. The HTTP method \(or verb\) and URI of the integration request are dictated by the backend \(that is, the integration endpoint\)\. They can be the same as or different from the method request's HTTP method and URI, respectively\. 

For example, when a Lambda function returns a file that is fetched from Amazon S3, you can expose this operation intuitively as a `GET` method request to the client even though the corresponding integration request requires that a `POST` request be used to invoke the Lambda function\. For an HTTP endpoint, it is likely that the method request and the corresponding integration request both use the same HTTP verb\. However, this is not required\. You can integrate the following method request: 

```
GET /{var}?query=value
Host: api.domain.net
```

With the following integration request: 

```
POST /
Host: service.domain.com
Content-Type: application/json
Content-Length: ...

{
   path: "{var}'s value",
   type: "value"
}
```

 As an API developer, you can use whatever HTTP verb and URI for a method request suit your requirements\. But you must follow the requirements of the integration endpoint\. When the method request data differs from the integration request data, you can reconcile the difference by providing mappings from the method request data to the integration request data\. 

In the preceding examples, the mapping translates the path variable \(`{var}`\) and the query parameter \(`query`\) values of the `GET` method request to the values of the integration request's payload properties of `path` and `type`\. Other mappable request data includes request headers and body\. These are described in [Set up request and response data mappings using the API Gateway console](how-to-method-settings-execution-console.md)\. 

When setting up the HTTP or HTTP proxy integration request, you assign the backend HTTP endpoint URL as the integration request URI value\. For example, in the PetStore API, the method request to get a page of pets has the following integration request URI: 

```
http://petstore-demo-endpoint.execute-api.com/petstore/pets
```

When setting up the Lambda or Lambda proxy integration, you assign the Amazon Resource Name \(ARN\) for invoking the Lambda function as the integration request URI value\. This ARN has the following format:

```
arn:aws:apigateway:api-region:lambda:path//2015-03-31/functions/arn:aws:lambda:lambda-region:account-id:function:lambda-function-name/invocations
```

The part after `arn:aws:apigateway:api-region:lambda:path/`, namely, `/2015-03-31/functions/arn:aws:lambda:lambda-region:account-id:function:lambda-function-name/invocations`, is the REST API URI path of the Lambda [Invoke](https://docs.aws.amazon.com/lambda/latest/dg/API_Invoke.html) action\. If you use the API Gateway console to set up the Lambda integration, API Gateway creates the ARN and assigns it to the integration URI after prompting you to choose the `lambda-function-name` from a region\. 

When setting up the integration request with another AWS service action, the integration request URI is also an ARN, similar to the integration with the Lambda `Invoke` action\. For example, for the integration with the [GetBucket](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketGET.html) action of Amazon S3, the integration request URI is an ARN of the following format:

```
arn:aws:apigateway:api-region:s3:path/{bucket}
```

The integration request URI is of the path convention to specify the action, where `{bucket}` is the placeholder of a bucket name\. Alternatively, an AWS service action can be referenced by its name\. Using the action name, the integration request URI for the `GetBucket` action of Amazon S3 becomes the following:

```
arn:aws:apigateway:api-region:s3:action/GetBucket
```

With the action\-based integration request URI, the bucket name \(`{bucket}`\) must be specified in the integration request body \(`{ Bucket: "{bucket}" }`\), following the input format of `GetBucket` action\. 

For AWS integrations, you must also configure [credentials](https://docs.aws.amazon.com/apigateway/api-reference/resource/integration/#credentials) to allow API Gateway to call the integrated actions\. You can create a new or choose an existing IAM role for API Gateway to call the action and then specify the role using its ARN\. The following shows an example of this ARN: 

```
arn:aws:iam::account-id:role/iam-role-name
```

This IAM role must contain a policy to allow the action to be executed\. It must also have API Gateway declared \(in the role's trust relationship\) as a trusted entity to assume the role\. Such permissions can be granted on the action itself\. They are known as resource\-based permissions\. For the Lambda integration, you can call the Lambda's [addPermission](https://docs.aws.amazon.com/lambda/latest/dg/API_AddPermission.html) action to set the resource\-based permissions and then set `credentials` to null in the API Gateway integration request\.

We discussed the basic integration setup\. Advanced settings involve mapping method request data to the integration request data\. After discussing the basic setup for an integration response, we cover advanced topics in [Set up request and response data mappings using the API Gateway console](how-to-method-settings-execution-console.md), where we also cover passing payload through and handling content encodings\.