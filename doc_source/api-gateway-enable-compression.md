# Enable Payload Compression for an API<a name="api-gateway-enable-compression"></a>

You can enable compression for an API using the API Gateway console, the AWS CLI, or an AWS SDK\.

For an existing API, you must deploy the API after enabling the compression in order for the change to take effect\. For a new API, you can deploy the API after the API setup is complete\.

**Topics**
+ [Enable Payload Compression for an API Using the API Gateway Console](#api-gateway-enable-compression-console)
+ [Enable Payload Compression for an API Using AWS CLI](#api-gateway-enable-compression-cli)
+ [Content Codings Supported by API Gateway](#api-gateway-supported-content-encodings)

## Enable Payload Compression for an API Using the API Gateway Console<a name="api-gateway-enable-compression-console"></a>

The following procedure describes how to enable payload compression for an API\. 

**To enable payload compression by using the API Gateway console**

1. Sign in to the API Gateway console\.

1. Choose an existing API or create a new one\.

1. In the primary navigation pane, choose **Settings** under the API you chose or the one you created\.

1. Under the **Content Encoding** section in the **Settings** pane, select the **Content Encoding enabled** option to enable payload compression\. Type a number for the minimum compression size \(in bytes\) next to **Minimum body size required for compression**\. To disable the compression, clear the **Content Encoding enabled** option\.

1. Choose **Save Changes**\.

## Enable Payload Compression for an API Using AWS CLI<a name="api-gateway-enable-compression-cli"></a>

To use the AWS CLI to create a new API and enable compression, call the [https://docs.aws.amazon.com/cli/latest/reference/apigateway/create-rest-api.html](https://docs.aws.amazon.com/cli/latest/reference/apigateway/create-rest-api.html) command as follows:

```
aws apigateway create-rest-api \
    --name "My test API" \
    --minimum-compression-size 0
```

To use the AWS CLI to enable compression on an existing API, call the [https://docs.aws.amazon.com/cli/latest/reference/apigateway/update-rest-api.html](https://docs.aws.amazon.com/cli/latest/reference/apigateway/update-rest-api.html) command as follows: 

```
aws apigateway update-rest-api \
    --rest-api-id 1234567890 \
    --patch-operations op=replace,path=/minimumCompressionSize,value=0
```

The `minimumCompressionSize` property has a non\-negative integer value between 0 and 10485760 \(10M bytes\)\. It measures the compression threshold\. If the payload size is smaller than this value, compression or decompression are not applied on the payload\. Setting it to zero allows compression for any payload size\.

To use the AWS CLI to disable compression, call the [https://docs.aws.amazon.com/cli/latest/reference/apigateway/update-rest-api.html](https://docs.aws.amazon.com/cli/latest/reference/apigateway/update-rest-api.html) command as follows: 

```
aws apigateway update-rest-api \
    --rest-api-id 1234567890 \
    --patch-operations op=replace,path=/minimumCompressionSize,value=
```

You can also set `value` to an empty string `""` or omit the `value` property altogether in the preceding call\.

## Content Codings Supported by API Gateway<a name="api-gateway-supported-content-encodings"></a>

API Gateway supports the following content codings:
+ `deflate`
+ `gzip`
+ `identity`

API Gateway also supports the following `Accept-Encoding` header format, according to the [RFC 7231](https://tools.ietf.org/html/rfc7231#section-5.3.4) specification:
+ `Accept-Encoding:deflate,gzip`
+ `Accept-Encoding:`
+ `Accept-Encoding:*`
+ `Accept-Encoding:deflate;q=0.5,gzip=1.0`
+ `Accept-Encoding:gzip;q=1.0,identity;q=0.5,*;q=0`

The highest priority content coding must be one supported by API Gateway\. If it is not compression is not applied to the response payload\.
