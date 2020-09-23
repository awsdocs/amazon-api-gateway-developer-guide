# Troubleshooting issues with HTTP API JWT authorizers<a name="http-api-troubleshooting-jwt"></a>

The following provides troubleshooting advice for errors and issues that you might encounter when using JSON Web Token \(JWT\) authorizers with HTTP APIs\.

## Issue: My API returns `401 {"message":"Unauthorized"}`<a name="http-api-troubleshooting-lambda-internal-server-error"></a>

Check the `www-authenticate` header in the response from the API\.

The following command uses `curl` to send a request to an API with a JWT authorizer that uses `$request.header.Authorization` as its identity source\.

```
$curl -v -H "Authorization: token" https://api-id.execute-api.us-west-2.amazonaws.com/route
```

The response from the API includes a `www-authenticate` header\.

```
...
< HTTP/1.1 401 Unauthorized
< Date: Wed, 13 May 2020 04:07:30 GMT
< Content-Length: 26
< Connection: keep-alive
< www-authenticate: Bearer scope="" error="invalid_token" error_description="the token does not have a valid audience"
< apigw-requestid: Mc7UVioPPHcEKPA=
<
* Connection #0 to host api-id.execute-api.us-west-2.amazonaws.com left intact
{"message":"Unauthorized"}}
```

In this case, the `www-authenticate` header shows that the token wasn't issued for a valid audience\. For API Gateway to authorize a request, the JWT's `aud` or `client_id` claim must match one of the audience entries that's configured for the authorizer\.

You can also decode a JWT and verify that it matches the issuer, audience, and scopes that your API requires\. The website [jwt\.io](https://jwt.io/) can debug JWTs in the browser\. The OpenID Foundation maintains a [list of libraries for working with JWTs](https://openid.net/developers/jwt/)\. 

To learn more about JWT authorizers, see [Controlling access to HTTP APIs with JWT authorizers](http-api-jwt-authorizer.md)\.