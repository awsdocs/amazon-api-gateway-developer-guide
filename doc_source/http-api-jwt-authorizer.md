# Controlling access to HTTP APIs with JWT authorizers<a name="http-api-jwt-authorizer"></a>

## <a name="http-api-jwt-authorizer.intro"></a>

You can use JSON Web Tokens \(JWTs\) as a part of [OpenID Connect \(OIDC\)](https://openid.net/specs/openid-connect-core-1_0.html) and [OAuth 2\.0](https://oauth.net/2/) frameworks to restrict client access to your APIs\.

If you configure a JWT authorizer for a route of your API, API Gateway validates the JWTs that clients submit with API requests\. API Gateway allows or denies requests based on token validation, and optionally, scopes in the token\. If you configure scopes for a route, the token must include at least one of the route's scopes\.

You can configure distinct authorizers for each route of an API, or use the same authorizer for multiple routes\.

**Note**  
There is no standard mechanism to differentiate JWT access tokens from other types of JWTs, such as OpenID Connect ID tokens\. Unless you require ID tokens for API authorization, we recommend that you configure your routes to require authorization scopes\. You can also configure your JWT authorizers to require issuers or audiences that your identity provider uses only when issuing JWT access tokens\.

## Authorizing API requests<a name="http-api-jwt-authorizer.evaluation"></a>

API Gateway uses the following general workflow to authorize requests to routes that are configured to use a JWT authorizer\. 

1. Check the [https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/apis-apiid-authorizers-authorizerid.html#apis-apiid-authorizers-authorizerid-prop-authorizer-identitysource](https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/apis-apiid-authorizers-authorizerid.html#apis-apiid-authorizers-authorizerid-prop-authorizer-identitysource) for a token\. The `identitySource` can include only the token, or the token prefixed with `Bearer `\.

1. Decode the token\.

1. Check the token's algorithm and signature by using the public key that is fetched from the issuer's `jwks_uri`\. Currently, only RSA\-based algorithms are supported\.

1. Validate claims\. API Gateway evaluates the following token claims:
   +  [https://tools.ietf.org/html/rfc7517#section-4.5](https://tools.ietf.org/html/rfc7517#section-4.5) – The token must have a header claim that matches the key in the `jwks_uri` that signed the token\.
   + [https://tools.ietf.org/html/rfc7519#section-4.1.1](https://tools.ietf.org/html/rfc7519#section-4.1.1) – Must match the [https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/apis-apiid-authorizers-authorizerid.html#apis-apiid-authorizers-authorizerid-model-jwtconfiguration](https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/apis-apiid-authorizers-authorizerid.html#apis-apiid-authorizers-authorizerid-model-jwtconfiguration) that is configured for the authorizer\.
   + [https://tools.ietf.org/html/rfc7519#section-4.1.3](https://tools.ietf.org/html/rfc7519#section-4.1.3) or `client_id` – Must match one of the [https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/apis-apiid-authorizers-authorizerid.html#apis-apiid-authorizers-authorizerid-model-jwtconfiguration](https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/apis-apiid-authorizers-authorizerid.html#apis-apiid-authorizers-authorizerid-model-jwtconfiguration) entries that is configured for the authorizer\.
   + [https://tools.ietf.org/html/rfc7519#section-4.1.4](https://tools.ietf.org/html/rfc7519#section-4.1.4) – Must be after the current time in UTC\. 
   + [https://tools.ietf.org/html/rfc7519#section-4.1.5](https://tools.ietf.org/html/rfc7519#section-4.1.5) – Must be before the current time in UTC\. 
   + [https://tools.ietf.org/html/rfc7519#section-4.1.6](https://tools.ietf.org/html/rfc7519#section-4.1.6) – Must be before the current time in UTC\. 
   + [https://tools.ietf.org/html/rfc6749#section-3.3](https://tools.ietf.org/html/rfc6749#section-3.3) or `scp` – The token must include at least one of the scopes in the route's [https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/apis-apiid-routes-routeid.html#apis-apiid-routes-routeid-prop-updaterouteinput-authorizationscopes](https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/apis-apiid-routes-routeid.html#apis-apiid-routes-routeid-prop-updaterouteinput-authorizationscopes)\.

If any of these steps fail, API Gateway denies the API request\.

After validating the JWT, API Gateway passes the claims in the token to the API route’s integration\. Backend resources, such as Lambda functions, can access the JWT claims in `$context.authorizer.claims`\. For example, if the JWT included an identity claim `emailID`, it's available in `$context.authorizer.claims.emailID`\.

## Create a JWT authorizer by using the AWS CLI<a name="http-api-jwt-authorizer.create"></a>

Before you create a JWT authorizer, you must register a client application with an identity provider\. You must also have created an HTTP API\. For examples of creating an HTTP API, see [Creating an HTTP API](http-api-develop.md#http-api-examples)\.

The following command creates a JWT authorizer that uses Amazon Cognito as an identity provider\.

```
aws apigatewayv2 create-authorizer \
    --name authorizer-name \
    --api-id api-id \
    --authorizer-type JWT \
    --identity-source '$request.header.Authorization' \
    --jwt-configuration Audience=audience,Issuer=https://cognito-idp.us-east-2.amazonaws.com/userPoolID
```

## Update a route to use a JWT authorizer by using the AWS CLI<a name="http-api-jwt-authorizer.create.route"></a>

The following command updates a route to use a JWT authorizer\.

```
aws apigatewayv2 update-route \
   --api-id api-id  \
   --route-id route-id  \
   --authorization-type JWT \
   --authorizer-id authorizer-id \
   --authorization-scopes user.id user.email
```