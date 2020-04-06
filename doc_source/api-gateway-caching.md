# Enabling API caching to enhance responsiveness<a name="api-gateway-caching"></a>

You can enable API caching in Amazon API Gateway to cache your endpoint's responses\. With caching, you can reduce the number of calls made to your endpoint and also improve the latency of requests to your API\. 

When you enable caching for a stage, API Gateway caches responses from your endpoint for a specified time\-to\-live \(TTL\) period, in seconds\. API Gateway then responds to the request by looking up the endpoint response from the cache instead of making a request to your endpoint\. The default TTL value for API caching is 300 seconds\. The maximum TTL value is 3600 seconds\. TTL=0 means caching is disabled\.

The maximum size of a response that can be cached is 1048576 bytes\. Cache data encryption may increase the size of the response when it is being cached\.

This is a HIPAA Eligible Service\. For more information about AWS, U\.S\. Health Insurance Portability and Accountability Act of 1996 \(HIPAA\), and using AWS services to process, store, and transmit protected health information \(PHI\), see [HIPAA Overview](https://aws.amazon.com/compliance/hipaa-compliance/)\.

**Important**  
When you enable caching for a stage, only `GET` methods have caching enabled by default\. This helps to ensure the safety and availability of your API\. You can enable caching for other methods by [overriding method settings](#override-api-gateway-stage-cache-for-method-cache)\.

**Important**  
Caching is charged by the hour and is not eligible for the AWS Free Tier\. 

## Enable Amazon API Gateway caching<a name="enable-api-gateway-caching"></a>

In API Gateway, you can enable caching for a specified stage\.

 When you enable caching, you must choose a cache capacity\. In general, a larger capacity gives a better performance, but also costs more\. 

 API Gateway enables caching by creating a dedicated cache instance\. This process can take up to 4 minutes\. 

API Gateway changes caching capacity by removing the existing cache instance and creating a new one with a modified capacity\. All existing cached data is deleted\. 

 In the API Gateway console, you configure caching in the **Settings** tab of a named **Stage Editor**\. 

**To configure API caching for a given stage:**

1. Go to the API Gateway console\.

1. Choose the API\.

1. Choose **Stages**\.

1. In the **Stages** list for the API, choose the stage\.

1. Choose the **Settings** tab\.

1. Choose **Enable API cache**\.

1. Wait for the cache creation to complete\. 

**Note**  
 Creating or deleting a cache takes about 4 minutes for API Gateway to complete\. When a cache is created, the **Cache status** value changes from `CREATE_IN_PROGRESS` to `AVAILABLE`\. When cache deletion is completed, the **Cache status** value changes from `DELETE_IN_PROGRESS` to an empty string\. 

When you enable caching within a stage's **Cache Settings**, only `GET` methods are cached\. To ensure the safety and availability of your API, we recommend that you don't change this setting\. However, you can enable caching for other methods by [overriding method settings](#override-api-gateway-stage-cache-for-method-cache)\.

 If you would like to verify if caching is functioning as expected, you have two general options: 
+  Inspect the CloudWatch metrics of **CacheHitCount** and **CacheMissCount** for your API and stage\.  
+  Put a timestamp in the response\. 

**Note**  
 You should not use the `X-Cache` header from the CloudFront response to determine if your API is being served from your API Gateway cache instance\. 

## Override API Gateway stage\-level caching for method caching<a name="override-api-gateway-stage-cache-for-method-cache"></a>

You can override stage\-level cache settings by enabling or disabling caching for a specific method\. By increasing or decreasing its TTL period; or by turning encryption on or off for cached responses\.

If you anticipate that a method that you are caching will receive sensitive data in its responses, in **Cache Settings**, choose **Encrypt cache data**\.

**To configure API caching for individual methods using the console:**

1. Sign in to the API Gateway console at [https://console\.aws\.amazon\.com/apigateway](https://console.aws.amazon.com/apigateway)\.

1. Go to the API Gateway console\.

1. Choose the API\.

1. Choose **Stages**\.

1. In the **Stages** list for the API, expand the stage and choose a method in the API\.

1. Choose **Override for this method** in **Settings**\.

1. In the **Cache Settings** area, you can set or clear **Enable Method Cache** or customize any other desired options\. \(This section is shown only if [stage\-level caching](#enable-api-gateway-caching) is enabled\.\)

## Use method or integration parameters as cache keys to index cached responses<a name="enable-api-gateway-cache-keys"></a>

 When a cached method or integration has parameters, which can take the form of custom headers, URL paths, or query strings, you can use some or all of the parameters to form cache keys\. API Gateway can cache the method's responses, depending on the parameter values used\. 

**Note**  
Cache keys are required when setting up caching on a resource\.

 For example, suppose you have a request in the following format: 

```
GET /users?type=... HTTP/1.1
host: example.com
...
```

In this request, `type` can take a value of `admin` or `regular`\. If you include the `type` parameter as part of the cache key, the responses from `GET /users?type=admin` are cached separately from those from `GET /users?type=regular`\. 

 When a method or integration request takes more than one parameter, you can choose to include some or all of the parameters to create the cache key\. For example, you can include only the `type` parameter in the cache key for the following request, made in the listed order within a TTL period: 

```
GET /users?type=admin&department=A HTTP/1.1
host: example.com
...
```

 The response from this request is cached and is used to serve the following request: 

```
GET /users?type=admin&department=B HTTP/1.1
host: example.com
...
```

 To include a method or integration request parameter as part of a cache key in the API Gateway console, select **Caching** after you add the parameter\. 

![\[Include method or integration parameters as cache keys to index cached response\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/api-caching-including-parameter-as-cache-key.png)

## Flush the API stage cache in API Gateway<a name="flush-api-caching"></a>

 When API caching is enabled, you can flush your API stage's entire cache to ensure your API's clients get the most recent responses from your integration endpoints\. 

 To flush the API stage cache, you can choose the **Flush entire cache** button under the **Cache Settings** section in the **Settings** tab in a stage editor of the API Gateway console\. The cache\-flushing operation takes a couple of minutes, after which the cache status is `AVAILABLE` immediately after flushing\. 

**Note**  
After the cache is flushed, responses are serviced from the integration endpoint until the cache is built up again\. During this period, the number of requests sent to the integration endpoint may increase\. This may temporarily increase the overall latency of your API\. 

## Invalidate an API Gateway cache entry<a name="invalidate-method-caching"></a>

A client of your API can invalidate an existing cache entry and reload it from the integration endpoint for individual requests\. The client must send a request that contains the `Cache-Control: max-age=0` header\. The client receives the response directly from the integration endpoint instead of the cache, provided that the client is authorized to do so\. This replaces the existing cache entry with the new response, which is fetched from the integration endpoint\. 

 To grant permission for a client, attach a policy of the following format to an IAM execution role for the user\. 

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

 This policy allows the API Gateway execution service to invalidate the cache for requests on the specified resource \(or resources\)\. To specify a group of targeted resources, use a wildcard \(\*\) character for `account-id`, `api-id`, and other entries in the ARN value of `Resource`\. For more information on how to set permissions for the API Gateway execution service, see [Control access to an API with IAM permissions](permissions.md)\. 

 If you don't impose an `InvalidateCache` policy \(or choose the **Require authorization** check box in the console\), any client can invalidate the API cache\. If most or all of the clients invalidate the API cache, this could significantly increase the latency of your API\. 

 When the policy is in place, caching is enabled and authorization is required\. You can control how unauthorized requests are handled by choosing an option from **Handle unauthorized requests** in the API Gateway console\. 

![\[Configure cache invalidation\]](http://docs.aws.amazon.com/apigateway/latest/developerguide/images/apig-cache-invalidation.png)

 The three options result in the following behaviors: 
+   **Fail the request with 403 status code**: returns a 403 Unauthorized response\. 

   To set this option using the API, use `FAIL_WITH_403`\. 
+   **Ignore cache control header; Add a warning in response header**: process the request and add a warning header in the response\. 

   To set this option using the API, use `SUCCEED_WITH_RESPONSE_HEADER`\. 
+   **Ignore cache control header**: process the request and do not add a warning header in the response\. 

   To set this option using the API, use `SUCCEED_WITHOUT_RESPONSE_HEADER`\. 