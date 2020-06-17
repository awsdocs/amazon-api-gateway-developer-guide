# Setting up a `$connect` route that requires a WebSocket subprotocol<a name="websocket-connect-route-subprotocol"></a>

Clients can use the `Sec-WebSocket-Protocol` field to request a [WebSocket subprotocol](https://tools.ietf.org/html/rfc6455#page-12) during the connection to your WebSocket API\. You can set up an integration for the `$connect` route to allow connections only if a client requests a subprotocol that your API supports\.

The following example Lambda function returns the `Sec-WebSocket-Protocol` header to clients\. The function establishes a connection to your API only if the client specifies the `myprotocol` subprotocol\.

For an AWS CloudFormation template that creates this example API and Lambda proxy integration, see [samples/ws-subprotocol.zip](samples/ws-subprotocol.zip)\.

```
exports.handler = async (event) => {
    if (event.headers != undefined) {
        const headers = toLowerCaseProperties(event.headers);
        
        if (headers['sec-websocket-protocol'] != undefined) {
            const subprotocolHeader = headers['sec-websocket-protocol'];
            const subprotocols = subprotocolHeader.split(',');
            
            if (subprotocols.indexOf('myprotocol') >= 0) {
                const response = {
                    statusCode: 200,
                    headers: {
                        "Sec-WebSocket-Protocol" : "myprotocol"
                    }
                };
                return response;
            }
        }
    }
    
    const response = {
        statusCode: 400
    };
        
    return response;
};

function toLowerCaseProperties(obj) {
    var wrapper = {};
    for (var key in obj) {
        wrapper[key.toLowerCase()] = obj[key];
    }
    return wrapper;
}
```

## <a name="websocket-connect-route-subprotocol-test"></a>

You can use [https://www.npmjs.com/package/wscat](https://www.npmjs.com/package/wscat) to test that your API allows connections only if a client requests a subprotocol that your API supports\. The following commands use the `-s` flag to specify subprotocols during the connection\.

The following command attempts a connection with an unsupported subprotocol\. Because the client specified the `chat1` subprotocol, the Lambda integration returns a 400 error, and the connection is unsuccessful\.

```
wscat -c wss://api-id.execute-api.region.amazonaws.com/beta -s chat1
error: Unexpected server response: 400
```

The following command includes a supported subprotocol in the connection request\. The Lambda integration allows the connection\.

```
wscat -c wss://api-id.execute-api.region.amazonaws.com/beta -s chat1,myprotocol
connected (press CTRL+C to quit)
```

To learn more about invoking WebSocket APIs, see [Invoking a WebSocket API](apigateway-how-to-call-websocket-api.md)\.