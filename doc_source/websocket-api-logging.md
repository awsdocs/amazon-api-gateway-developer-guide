# Configuring logging for a WebSocket API<a name="websocket-api-logging"></a>

## <a name="websocket-api-logging.intro"></a>

You can enable logging to write logs to CloudWatch Logs\. You can use logging variables to customize the content of your logs\.

For instructions on how to set up CloudWatch logging, see [Set up CloudWatch API logging using the API Gateway console](set-up-logging.md#set-up-access-logging-using-console)\.

When you specify the **Log Format**, you can choose which context variables to log\. The following variables are supported\.


| Parameter | Description | 
| --- | --- | 
| $context\.apiId |  The identifier API Gateway assigns to your API\.  | 
| $context\.authorize\.error | The authorization error message\. | 
| $context\.authorize\.latency | The authorization latency in ms\. | 
| $context\.authorize\.status | The status code returned from an authorization attempt\. | 
| $context\.authorizer\.error | The error message returned from an authorizer\. | 
| $context\.authorizer\.integrationLatency | The Lambda authorizer latency in ms\. | 
| $context\.authorizer\.integrationStatus | The status code returned from a Lambda authorizer\. | 
| $context\.authorizer\.latency | The authorizer latency in ms\. | 
| $context\.authorizer\.requestId | The AWS endpoint's request ID\. | 
| $context\.authorizer\.status | The status code returned from an authorizer\. | 
| $context\.authorizer\.principalId |  The principal user identification that is associated with the token sent by the client and returned from an API Gateway Lambda authorizer Lambda function\. \(A Lambda authorizer was formerly known as a custom authorizer\.\)  | 
| $context\.authorizer\.property |  The stringified value of the specified key\-value pair of the `context` map returned from an API Gateway Lambda authorizer function\. For example, if the authorizer returns the following `context` map:  <pre>"context" : {<br />                            "key": "value",<br />                            "numKey": 1,<br />                            "boolKey": true<br />                            }</pre> calling `$context.authorizer.key` returns the `"value"` string, calling `$context.authorizer.numKey` returns the `"1"` string, and calling `$context.authorizer.boolKey` returns the `"true"` string\.  | 
| $context\.authenticate\.error | The error message returned from an authentication attempt\. | 
| $context\.authenticate\.latency | The authentication latency in ms\. | 
| $context\.authenticate\.status | The status code returned from an authentication attempt\. | 
| $context\.connectedAt |  The [Epoch](https://en.wikipedia.org/wiki/Unix_time)\-formatted connection time\.  | 
| $context\.connectionId |  A unique ID for the connection that can be used to make a callback to the client\.  | 
| $context\.domainName |  A domain name for the WebSocket API\. This can be used to make a callback to the client \(instead of a hardcoded value\)\.  | 
| $context\.error\.message |  A string that contains an API Gateway error message\.  | 
| $context\.error\.messageString | The quoted value of $context\.error\.message, namely "$context\.error\.message"\. | 
| $context\.error\.responseType |  The error response type\.  | 
| $context\.error\.validationErrorString |  A string that contains a detailed validation error message\.  | 
| $context\.eventType |  The event type: `CONNECT`, `MESSAGE`, or `DISCONNECT`\.  | 
| $context\.extendedRequestId | An automatically generated ID for the API call, which contains more useful information for debugging/troubleshooting\. | 
| $context\.identity\.accountId |  The AWS account ID associated with the request\.  | 
| $context\.identity\.apiKey |  The API owner key associated with key\-enabled API request\.  | 
| $context\.identity\.apiKeyId | The API key ID associated with the key\-enabled API request | 
| $context\.identity\.caller |  The principal identifier of the caller making the request\.  | 
| $context\.identity\.cognitoAuthenticationProvider |  A comma\-separated list of the Amazon Cognito authentication providers used by the caller making the request\. Available only if the request was signed with Amazon Cognito credentials\. For information related to this and the other Amazon Cognito `$context` variables, see [Using Federated Identities](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-identity.html) in the *Amazon Cognito Developer Guide*\.  | 
| $context\.identity\.sourceIp |  The source IP address of the TCP connection making the request to API Gateway\.  | 
| $context\.identity\.user |  The principal identifier of the user making the request\.  | 
| $context\.identity\.userAgent |  The user agent of the API caller\.  | 
| $context\.identity\.userArn |  The Amazon Resource Name \(ARN\) of the effective user identified after authentication\.  | 
| $context\.integration\.error | The error message returned from an integration\. Equivalent to $context\.integrationErrorMessage\. | 
| $context\.integration\.integrationStatus | For Lambda proxy integration, the status code returned from AWS Lambda, not from the backend Lambda function code\. | 
| $context\.integration\.latency | The integration latency in ms\. Equivalent to $context\.integrationLatency\. | 
| $context\.integration\.requestId | The AWS endpoint's request ID\. Equivalent to $context\.awsEndpointRequestId\. | 
| $context\.integration\.status | The status code returned from an integration\. For Lambda proxy integrations, this is the status code that your Lambda function code returns\. Equivalent to $context\.integrationStatus\. | 
| $context\.integrationLatency | The integration latency in ms, available for access logging only\. | 
| $context\.messageId |  A unique server\-side ID for a message\. Available only when the `$context.eventType` is `MESSAGE`\.  | 
| $context\.requestId |  Same as `$context.extendedRequestId`\.  | 
| $context\.requestTime | The [CLF](https://httpd.apache.org/docs/1.3/logs.html#common)\-formatted request time \(dd/MMM/yyyy:HH:mm:ss \+\-hhmm\)\. | 
| $context\.requestTimeEpoch | The [Epoch](https://en.wikipedia.org/wiki/Unix_time)\-formatted request time\. | 
| $context\.routeKey |  The selected route key\.  | 
| $context\.stage |  The deployment stage of the API call \(for example, beta or prod\)\.  | 
| $context\.status |  The response status\.  | 
| $context\.waf\.error | The error message returned from AWS WAF\. | 
| $context\.waf\.latency | The AWS WAF latency in ms\. | 
| $context\.waf\.status | The status code returned from AWS WAF\. | 