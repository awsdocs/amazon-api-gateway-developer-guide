# Use `@connections` commands in your backend service<a name="apigateway-how-to-call-websocket-api-connections"></a>

Your backend service can use the following WebSocket connection HTTP requests to send a callback message to a connected client, get connection information, or disconnect the client\.

**Important**  
These requests use [IAM authorization](apigateway-websocket-control-access-iam.md), so you must sign them with [Signature Version 4 \(SigV4\)](https://docs.aws.amazon.com/general/latest/gr/sigv4_signing.html)\. To do this, you can use the API Gateway Management API\. For more information, see [ApiGatewayManagementApi](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/apigatewaymanagementapi.html)\.

In the following command, you need to replace `{api-id}` with the actual API ID, which is displayed in the API Gateway console or returned by the AWS CLI [https://docs.aws.amazon.com/goto/aws-cli/apigatewayv2-2018-11-29/CreateApi](https://docs.aws.amazon.com/goto/aws-cli/apigatewayv2-2018-11-29/CreateApi) command\. In addition, if your API is in a Region other than `us-east-1`, you need to substitute the correct Region\.

To send a callback message to the client, use:

```
POST https://{api-id}.execute-api.us-east-1.amazonaws.com/{stage}/@connections/{connection_id}
```

You can test this request by using `[Postman](http://www.postman.com/)` or by calling `[awscurl](https://github.com/okigan/awscurl)` as in the following example:

```
awscurl --service execute-api -X POST -d "hello world" https://{prefix}.execute-api.us-east-1.amazonaws.com/{stage}/@connections/{connection_id}
```

You need to URL\-encode the command as in the following example:

```
awscurl --service execute-api -X POST -d "hello world" https://aabbccddee.execute-api.us-east-1.amazonaws.com/prod/%40connections/R0oXAdfD0kwCH6w%3D
```

To get the latest connection status of the client, use:

```
GET https://{api-id}.execute-api.us-east-1.amazonaws.com/{stage}/@connections/{connection_id}
```

To disconnect the client, use:

```
DELETE https://{api-id}.execute-api.us-east-1.amazonaws.com/{stage}/@connections/{connection_id}
```

You can dynamically build a callback URL by using the `$context` variables in your integration\. For example, if you use Lambda proxy integration with a `Node.js` Lambda function, you can build the URL as follows:

```
exports.handler = function(event, context, callback) {
var domain = event.requestContext.domainName;
var stage = event.requestContext.stage;
var connectionId = event.requestContext.connectionId;
var callbackUrl = util.format(util.format('https://%s/%s/@connections/%s', domain, stage, connectionId));
// Do a SigV4 and then make the call
}
```