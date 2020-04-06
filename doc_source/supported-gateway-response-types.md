# Gateway response types<a name="supported-gateway-response-types"></a>

 API Gateway exposes the following gateway responses for customization by API developers\. 


| Gateway response type | Default status code | Description | 
| --- | --- | --- | 
| ACCESS\_DENIED | 403 | The gateway response for authorization failure—for example, when access is denied by a custom or Amazon Cognito authorizer\. If the response type is unspecified, this response defaults to the `DEFAULT_4XX` type\. | 
| API\_CONFIGURATION\_ERROR | 500 | The gateway response for an invalid API configuration—including when an invalid endpoint address is submitted, when base64 decoding fails on binary data when binary support is enacted, or when integration response mapping can't match any template and no default template is configured\. If the response type is unspecified, this response defaults to the `DEFAULT_5XX` type\. | 
| AUTHORIZER\_CONFIGURATION\_ERROR | 500 | The gateway response for failing to connect to a custom or Amazon Cognito authorizer\. If the response type is unspecified, this response defaults to the `DEFAULT_5XX` type\. | 
| AUTHORIZER\_FAILURE | 500 | The gateway response when a custom or Amazon Cognito authorizer failed to authenticate the caller\. If the response type is unspecified, this response defaults to the `DEFAULT_5XX` type\. | 
| BAD\_REQUEST\_PARAMETERS | 400 | The gateway response when the request parameter cannot be validated according to an enabled request validator\. If the response type is unspecified, this response defaults to the `DEFAULT_4XX` type\. | 
| BAD\_REQUEST\_BODY | 400 | The gateway response when the request body cannot be validated according to an enabled request validator\. If the response type is unspecified, this response defaults to the `DEFAULT_4XX` type\. | 
| DEFAULT\_4XX |  Null | The default gateway response for an unspecified response type with the status code of `4XX`\. Changing the status code of this fallback gateway response changes the status codes of all other `4XX` responses to the new value\. Resetting this status code to null reverts the status codes of all other `4XX` responses to their original values\. | 
| DEFAULT\_5XX | Null | The default gateway response for an unspecified response type with a status code of `5XX`\. Changing the status code of this fallback gateway response changes the status codes of all other `5XX` responses to the new value\. Resetting this status code to null reverts the status codes of all other `5XX` responses to their original values\. | 
| EXPIRED\_TOKEN | 403 | The gateway response for an AWS authentication token expired error\. If the response type is unspecified, this response defaults to the `DEFAULT_4XX` type\. | 
| INTEGRATION\_FAILURE | 504 | The gateway response for an integration failed error\. If the response type is unspecified, this response defaults to the `DEFAULT_5XX` type\. | 
| INTEGRATION\_TIMEOUT | 504 | The gateway response for an integration timed out error\. If the response type is unspecified, this response defaults to the `DEFAULT_5XX` type\. | 
| INVALID\_API\_KEY | 403 | The gateway response for an invalid API key submitted for a method requiring an API key\. If the response type is unspecified, this response defaults to the `DEFAULT_4XX` type\.  | 
| INVALID\_SIGNATURE | 403 | The gateway response for an invalid AWS signature error\. If the response type is unspecified, this response defaults to the `DEFAULT_4XX` type\. | 
| MISSING\_AUTHENTICATION\_TOKEN | 403 | The gateway response for a missing authentication token error, including the cases when the client attempts to invoke an unsupported API method or resource\. If the response type is unspecified, this response defaults to the `DEFAULT_4XX` type\. | 
| QUOTA\_EXCEEDED | 429 | The gateway response for the usage plan quota exceeded error\. If the response type is unspecified, this response defaults to the `DEFAULT_4XX` type\. | 
| REQUEST\_TOO\_LARGE | 413 | The gateway response for the request too large error\. If the response type is unspecified, this response defaults to the `DEFAULT_4XX` type\. | 
| RESOURCE\_NOT\_FOUND | 404 | The gateway response when API Gateway cannot find the specified resource after an API request passes authentication and authorization, except for API key authentication and authorization\. If the response type is unspecified, this response defaults to the `DEFAULT_4XX` type\. | 
| THROTTLED | 429 | The gateway response when usage plan\-, method\-, stage\-, or account\-level throttling limits exceeded\. If the response type is unspecified, this response defaults to the `DEFAULT_4XX` type\. | 
| UNAUTHORIZED | 401 | The gateway response when the custom or Amazon Cognito authorizer failed to authenticate the caller\. | 
| UNSUPPORTED\_MEDIA\_TYPE | 415 | The gateway response when a payload is of an unsupported media type, if strict passthrough behavior is enabled\. If the response type is unspecified, this response defaults to the `DEFAULT_4XX` type\. | 
| WAF\_FILTERED | 403 | The gateway response when a request is blocked by AWS WAF\. If the response type is unspecified, this response defaults to the `DEFAULT_4XX` type\. | 