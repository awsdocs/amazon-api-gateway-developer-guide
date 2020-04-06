# Use `wscat` to connect to a WebSocket API and send messages to it<a name="apigateway-how-to-call-websocket-api-wscat"></a>

The `[wscat](https://www.npmjs.com/package/wscat)` utility is a convenient tool for testing a WebSocket API that you have created and deployed in API Gateway\. You can install and use `wscat` as follows:

1. Download `wscat` from [https://www\.npmjs\.com/package/wscat](https://www.npmjs.com/package/wscat)\.

1. Install `wscat` by running the following command:

   ```
   npm install -g wscat
   ```

1. To connect to your API, run the `wscat` command as shown in the following example\. Note that this example assumes that the `Authorization` setting is `NONE`\.

   ```
   wscat -c wss://aabbccddee.execute-api.us-east-1.amazonaws.com/test/
   ```

   You need to replace `aabbccddee` with the actual API ID, which is displayed in the API Gateway console or returned by the AWS CLI [https://docs.aws.amazon.com/goto/aws-cli/apigatewayv2-2018-11-29/CreateApi](https://docs.aws.amazon.com/goto/aws-cli/apigatewayv2-2018-11-29/CreateApi) command\.

   In addition, if your API is in a Region other than `us-east-1`, you need to substitute the correct Region\.

1. To test your API, enter a message such as the following while connected:

   ```
   {"{jsonpath-expression}":"{route-key}"}
   ```

   where *\{jsonpath\-expression\}* is a JSONPath expression and *\{route\-key\}* is a route key for the API\. For example:

   ```
   {"action":"action1"}
   {"message":"test response body"}
   ```

   For more information about JSONPath, see [JSONPath](http://goessner.net/articles/JsonPath/) or [JSONPath for Java](https://github.com/jayway/JsonPath)\.

1. To disconnect from your API, enter `ctrl-C`\.