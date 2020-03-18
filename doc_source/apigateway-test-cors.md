# Testing CORS<a name="apigateway-test-cors"></a>

You can test CORS by customizing the following `cURL` command for the actual method and origin header you are using:

```
curl -v -X OPTIONS -H "Access-Control-Request-Method: POST" -H "Origin: http://example.com" https://{restapi_id}.execute-api.{region}.amazonaws.com/{stage_name}
```