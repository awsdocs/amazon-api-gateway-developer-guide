# JWT Authorizers<a name="http-api-jwt-authorizer"></a>


|  | 
| --- |
| HTTP APIs are in beta for Amazon API Gateway and are subject to change\. | 

## <a name="http-api-jwt-authorizer.intro"></a>

You can use JSON Web Tokens \(JWTs\) as a part of [OpenID Connect \(OIDC\)](https://openid.net/specs/openid-connect-core-1_0.html) and [OAuth 2\.0](https://oauth.net/2/) frameworks to restrict client access to your APIs\.

If you configure a JWT authorizer for a route of your API, API Gateway validates the JWTs that clients submit with API requests\. API Gateway allows or denies requests based on token validation, and optionally, scopes in the token\.

You can configure distinct authorizers for each route of an API, or use the same authorizer for multiple routes\.

## Authorizing API Requests<a name="http-api-jwt-authorizer.evaluation"></a>

API Gateway uses the following general workflow to authorize requests to routes configured to use a JWT authorizer\. 

1. Check the route's `identitySource` for a token\.

1. Decode the token\.

1. Check the token's algorithm and signature using the public key fetched from the issuer's `jwks_uri`\. The `none` algorithm isn't supported\. 

1. Validate claims\. API Gateway evaluates the following token claims:
+ [https://tools.ietf.org/html/rfc7519#section-4.1.1](https://tools.ietf.org/html/rfc7519#section-4.1.1)
+ [https://tools.ietf.org/html/rfc7519#section-4.1.3](https://tools.ietf.org/html/rfc7519#section-4.1.3)
+ [https://tools.ietf.org/html/rfc7519#section-4.1.4](https://tools.ietf.org/html/rfc7519#section-4.1.4) 
+ [https://tools.ietf.org/html/rfc7519#section-4.1.5](https://tools.ietf.org/html/rfc7519#section-4.1.5) 
+ [https://tools.ietf.org/html/rfc6749#section-3.3](https://tools.ietf.org/html/rfc6749#section-3.3) 

If any of these steps fail, API Gateway denies the API request\.

After validating the JWT, API Gateway passes the claims in the token to the API route’s integration\. Backend resources, such as Lambda functions, can access the JWT claims in `$context.authorizer.claims`\. For example, if the JWT included an identity claim `emailID`, it's available in `$context.authorizer.claims.emailID`\.

## Create a JWT Authorizer by Using the AWS CLI<a name="http-api-jwt-authorizer.create"></a>

Before you create a JWT authorizer, you must register a client application with an identity provider\. You must also have created an HTTP API\. For examples of creating an HTTP API, see [Creating an HTTP API](http-api-examples.md)\.

The following command creates a JWT authorizer that uses Amazon Cognito as an identity provider\.

```
aws apigatewayv2 create-authorizer \
    --name authorizer-name \
    --api-id api-id \
    --authorizer-type JWT \
    --identity-source '$request.header.Authorization' \
    --jwt-configuration "Issuer='https://cognito-idp.us-east-2.amazonaws.com/userPoolID', Audience='audience'"
```

## Update a Route to Use a JWT Authorizer by Using the AWS CLI<a name="http-api-jwt-authorizer.create.route"></a>

The following command updates a route to use a JWT authorizer\.

```
aws apigatewayv2 update-route \
   --api-id api-id  \
   --route-id route-id  \
   --authorization-type JWT \
   --authorizer-id authorizer-id \
   --authorization-scopes user.id user.email
```