# Return binary media from a Lambda proxy integration<a name="lambda-proxy-binary-media"></a>

To return binary media from an [AWS Lambda proxy integration](set-up-lambda-proxy-integrations.md), base64 encode the response from your Lambda function\. You must also [configure your API's binary media types](api-gateway-payload-encodings-configure-with-console.md)\.

**Note**  
To use a web browser to invoke an API with this example integration, set your API's binary media types to `*/*`\. API Gateway uses the first `Accept` header from clients to determine if a response should return binary media\. To return binary media when you can't control the order of `Accept` header values, such as requests from a browser, set your API's binary media types to `*/*` \(for all content types\)\.

The following example Python 3 Lambda function can return a binary image from Amazon S3 or text to clients\. The function's response includes a `Content-Type` header to indicate to the client the type of data that it returns\. The function conditionally sets the `isBase64Encoded` property in its response, depending on the type of data that it returns\.

```
import base64
import boto3
import json
import random

s3 = boto3.client('s3')

def lambda_handler(event, context):
    number = random.randint(0,1)
    if number == 1:
        response = s3.get_object(
            Bucket='bucket-name',
            Key='image.png',
        )
        image = response['Body'].read()
        return {
            'headers': { "Content-Type": "image/png" },
            'statusCode': 200,
            'body': base64.b64encode(image),
            'isBase64Encoded': True
        }
    else:
        return {
            'headers': { "Content-type": "text/html" },
            'statusCode': 200,
            'body': "<h1>This is text</h1>",
        }
```

To learn more about binary media types, see [Working with binary media types for REST APIs](api-gateway-payload-encodings.md)\.