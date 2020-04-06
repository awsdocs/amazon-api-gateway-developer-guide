# Working with binary media types for WebSocket APIs<a name="websocket-api-develop-binary-media-types"></a>

API Gateway WebSocket APIs don't currently support binary frames in incoming message payloads\. If a client app sends a binary frame, API Gateway rejects it and disconnects the client with code 1003\.

There is a workaround for this behavior\. If the client sends a text\-encoded binary data \(e\.g\., base64\) as a text frame, you can set the integration's `contentHandlingStrategy` property to `CONVERT_TO_BINARY` to convert the payload from base64\-encoded string to binary\. 

To return a route response for a binary payload in non\-proxy integrations, you can set the integration response's `contentHandlingStrategy` property to `CONVERT_TO_TEXT` to convert the payload from binary to base64\-encoded string\.