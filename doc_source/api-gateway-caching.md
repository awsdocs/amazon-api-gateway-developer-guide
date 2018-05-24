# Enable API Caching to Enhance Responsiveness<a name="api-gateway-caching"></a>

You can enable API caching in Amazon API Gateway to cache your endpoint’s response\. With caching, you can reduce the number of calls made to your endpoint and also improve the latency of the requests to your API\. When you enable caching for a stage, API Gateway caches responses from your endpoint for a specified time\-to\-live \(TTL\) period, in seconds\. API Gateway then responds to the request by looking up the endpoint response from the cache instead of making a request to your endpoint\. The default TTL value for API caching is 300 seconds\. The maximum TTL value is 3600 seconds\. TTL=0 means caching is disabled\.

**Important**  
Only `GET` methods should be cached\.

**Note**  
Caching is charged by the hour and is not eligible for the AWS free tier\. 

## Enable Amazon API Gateway Caching<a name="enable-api-gateway-caching"></a>

 In API Gateway, you can enable caching for all methods for a specified stage\. When you enable caching, you must choose a cache capacity\. In general, a larger capacity gives a better performance, but also costs more\. 

 API Gateway enables caching by creating a dedicated cache instance\. This process can take up to 4 minutes\. 

API Gateway changes caching capacity by removing the existing cache instance and recreating a new one with a modified capacity\. All existing cached data is deleted\. 

 In the API Gateway console, you configure caching in the **Settings** tab of a named **Stage Editor**\. 

**To configure API caching for a given stage:**

1. Go to the API Gateway console\.

1. Navigate to the **Stage Editor** for the stage for which you want to enable caching\.

1. Choose **Settings**\.

1. Select **Enable API cache**\.

1. Wait for the cache creation to complete\. 

**Note**  
 Creating or deleting a cache takes about 4 minutes for API Gateway to complete\. When cache is created, the **Cache status** value changes from `CREATE_IN_PROGRESS` to `AVAILABLE`\. When cache deletion is completed, the **Cache status** value changes from `DELETE_IN_PROGRESS` to an empty string\. 

 When you enable caching within a stage's **Cache Settings**, you enable caching for all methods in that stage\. 

 If you would like to verify if caching is functioning as expected, you have two general options: 
+  Inspect the CloudWatch metrics of **CacheHitCount** and **CacheMissCount** for your API and stage\.  
+  Put a timestamp in the response\. 

**Note**  
 You should not use the X\-Cache header from the CloudFront response to determine if your API is being served from your API Gateway cache instance\. 

## Override API Gateway Stage\-Level Caching for Method Caching<a name="override-api-gateway-stage-cache-for-method-cache"></a>

 If you want more granularity in your caching settings, you can override the stage\-level caching for individual methods \. This includes disabling caching for a specific method, increasing or decreasing its TTL period, and turning on or off encryption of the cached response\. If you anticipate that a method will receive sensitive data in its responses, in **Cache Settings**, choose **Encrypt cache data**\. 

**To configure API caching for individual methods using the console:**

1. Choose **Stages** of an API from the main navigation pane\.

1. Choose a method of the API in the chosen stage, from the secondary navigation pane\.

1. Choose **Override for this method** in **Settings**\.

1. Choose appropriate settings under the **Cache Settings** section \(that is shown only if the stage\-level caching is enabled\)\. 

## Use Method or Integration Parameters as Cache Keys to Index Cached Responses<a name="enable-api-gateway-cache-keys"></a>

 When a cached method or integration has parameters, which can take the form of custom headers, URL paths, or query strings, you can use some or all of the parameters to form cache keys\. API Gateway can cache the method's responses, depending on the parameter values used\. 

 For example, suppose you have a request of the following format: 

```
GET /users?type=... HTTP/1.1
host: example.com
...
```

In this request, `type` can take a value of `admin` or `regular`\. If you include the `type` parameter as part of the cache key, the responses from `GET /users?type=admin` will be cached separately from those from `GET /users?type=regular`\. 

 When a method or integration request takes more than one parameter, you can choose to include some or all of the parameters to create the cache key\. For example, you can include only the `type` parameter in the cache key for the following request, made in the listed order within a TTL period: 

```
GET /users?type=admin&department=A HTTP/1.1
host: example.com
...
```

 The response from this request will be cached and will be used to serve the following request: 

```
GET /users?type=admin&department=B HTTP/1.1
host: example.com
...
```

 To include a method or integration request parameter as part of a cache key in the API Gateway console, select **Caching** after you add the parameter\. 

![\[Include Method or Integration Parameters as Cache Keys to Index Cached Response\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-caching-including-parameter-as-cache-key.png)

## Flush the API Stage Cache in API Gateway<a name="flush-api-caching"></a>

 When API caching is enabled, you can flush your API stage's entire cache to ensure your API's clients get the most recent responses from your integration endpoints\. 

 To flush the API stage cache, you can choose the **Flush entire cache** button under the **Cache Settings** section in the **Settings** tab in a stage editor of the API Gateway console\. The cache\-flushing operation is almost instantaneous\. As a result, the cache status is `AVAILABLE` immediately after flushing\. 

Notice that flushing the cache will cause the responses to ensuing requests to be serviced from the backend until the cache is built up again\. During this period, the number of requests sent to the integration endpoint may increase\. That may affect the overall latency of your API\. 

## Invalidate an API Gateway Cache Entry<a name="invalidate-method-caching"></a>

A client of your API can invalidate an existing cache entry and reloads it from the integration endpoint for individual requests\. The client must send a request that contains the `Cache-Control: max-age=0` header\. The client receives the response directly from the integration endpoint instead of the cache, provided that the user is authorized to do so\. This replaces the existing cache entry with the new response, which is fetched from the integration endpoint\. 

 To grant permission for a caller, attach a policy of the following format to an IAM execution role for the user\. 

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "execute-api:InvalidateCache"
      ],
      "Resource": [
        "arn:aws:execute-api:region:account-id:api-id/stage-name/GET/resource-path-specifier"
      ]
    }
  ]
}
```

 This policy allows the API Gateway execution service to invalidate cache for requests on the specified resource \(or resources\)\. To specify a group of targeted resources, use a wild card \(\*\) character for `account-id`, `api-id`, and other entries in the ARN value of `Resource`\. For more information on how to set permissions for the API Gateway execution service, see [Control Access to an API with IAM Permissions](permissions.md) 

 If you do not impose an `InvalidateCache` policy, any client can invalidate the API cache\. If all or most of the clients invalidate the API cache, there could be significant latency impact on your API\. 

 When the policy is in place, caching is enabled, and authorization is required, you can control how unauthorized requests are handled by choosing an option from **Handle unauthorized requests** in the API Gateway console\. 

![\[Configure cache invalidation\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/apig-cache-invalidation.png)

 The three options result in the following behaviors: 
+   **Fail the request with 403 status code**: returns a 403 Unauthorized response\. 

   To set this option using the API, use `FAIL_WITH_403`\. 
+   **Ignore cache control header; Add a warning in response header**: process the request and add a warning header in the response\. 

   To set this option using the API, use `SUCCEED_WITH_RESPONSE_HEADER`\. 
+   **Ignore cache control header**: process the request and do not add a warning header in the response\. 

   To set this option using the API, use `SUCCEED_WITHOUT_RESPONSE_HEADER`\. 