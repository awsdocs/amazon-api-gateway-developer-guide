# Choosing a minimum TLS version for a custom domain in API Gateway<a name="apigateway-custom-domain-tls-version"></a>

For greater security, you can choose a minimum Transport Layer Security \(TLS\) protocol version to be enforced for your Amazon API Gateway custom domain by setting a security policy in the API Gateway console, AWS CLI, or AWS SDKs\.

A *security policy* is a predefined combination of minimum TLS version and cipher suite offered by Amazon API Gateway\. You can choose either a TLS version 1\.2 or TLS version 1\.0 security policy\. The TLS protocol addresses network security problems such as tampering and eavesdropping between a client and server\. When your clients establish a TLS handshake to your API through the custom domain, the security policy enforces the TLS version and cipher suite options your clients can choose to use\.

In custom domain settings, a security policy determines two settings:
+ The minimum TLS version that API Gateway uses to communicate with API clients
+ The cipher that API Gateway uses to encrypt the content that it returns to API clients

**Topics**
+ [How to specify a minimum TLS protocol version for custom domains in API Gateway](#apigateway-custom-domain-tls-version-how-to)
+ [Supported security policies, TLS protocol versions, and ciphers for edge\-optimized API endpoints in API Gateway](#apigateway-custom-domain-tls-version-edge-optimized)
+ [Supported SSL/TLS protocols and ciphers for regional, private, and WebSocket API endpoints in API Gateway](#apigateway-custom-domain-tls-version-regional-and-websocket)
+ [OpenSSL and RFC cipher names](#apigateway-secure-connections-openssl-rfc-cipher-names)

## How to specify a minimum TLS protocol version for custom domains in API Gateway<a name="apigateway-custom-domain-tls-version-how-to"></a>

When you create a custom domain, you specify the security policy for it\. For more information about security policies, see the tables in the following sections\.

The following sections describe how to create a custom domain name, including specifying the minimum TLS version in the API Gateway console and CLI:
+ [Creating an edge\-optimized custom domain name](how-to-edge-optimized-custom-domain-name.md)
+ [Setting up a regional custom domain name in API Gateway](apigateway-regional-api-custom-domain-create.md)

You can change the security policy by updating the domain name settings\. To change the minimum TLS version, use one of the following commands, specifying the new TLS version \(`TLS_1_0` or `TLS_1_2`\) in the `securityPolicy` parameter\. Allow up to 60 minutes for the update to be completed\.
+ [https://docs.aws.amazon.com/apigateway/api-reference/link-relation/domainname-update/](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/domainname-update/)
+ [https://docs.aws.amazon.com/cli/latest/reference/apigateway/update-domain-name.html](https://docs.aws.amazon.com/cli/latest/reference/apigateway/update-domain-name.html)
+ [https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/domainnames-domainname.html#domainnames-domainnamepatch](https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/domainnames-domainname.html#domainnames-domainnamepatch)

## Supported security policies, TLS protocol versions, and ciphers for edge\-optimized API endpoints in API Gateway<a name="apigateway-custom-domain-tls-version-edge-optimized"></a>

The following table lists the protocols and ciphers that API Gateway can use for each security policy for edge\-optimized APIs\.


****  

|  | Security policy |  | TLS\-1\-0 | TLS\-1\-2 | SSL/TLS protocols supported | 
| --- | --- | --- | --- | --- | --- | 
| TLSv1\.2 | ♦ | ♦ | 
| TLSv1\.1 | ♦ |  | 
| TLSv1 | ♦ |  | 
| SSLv3 |  |  | 
| Ciphers supported | 
| ECDHE\-RSA\-AES128\-GCM\-SHA256 | ♦ | ♦ | 
| ECDHE\-RSA\-AES128\-SHA256 | ♦ | ♦ | 
| ECDHE\-RSA\-AES128\-SHA | ♦ |  | 
| ECDHE\-RSA\-AES256\-GCM\-SHA384 | ♦ | ♦ | 
| ECDHE\-RSA\-AES256\-SHA384 | ♦ | ♦ | 
| ECDHE\-RSA\-AES256\-SHA | ♦ |  | 
| AES128\-GCM\-SHA256 | ♦ | ♦ | 
| AES256\-GCM\-SHA384 | ♦ | ♦ | 
| AES128\-SHA256 | ♦ | ♦ | 
| AES256\-SHA | ♦ |  | 
| AES128\-SHA | ♦ |  | 
| DES\-CBC3\-SHA  | ♦ |  | 
| RC4\-MD5 |  |  | 

## Supported SSL/TLS protocols and ciphers for regional, private, and WebSocket API endpoints in API Gateway<a name="apigateway-custom-domain-tls-version-regional-and-websocket"></a>

The following table describes the security policies that can be specified for Regional, private, and WebSocket API endpoints\.

**Note**  
For private and WebSocket APIs, only `TLS-1-2` can be specified\.


| Security policy | TLS\-1\-0 | TLS\-1\-2 | 
| --- | --- | --- | 
| TLS Protocols | 
| Protocol\-TLSv1 | ♦ |  | 
| Protocol\-TLSv1\.1 | ♦ |  | 
| Protocol\-TLSv1\.2 | ♦ | ♦ | 
| TLS Ciphers | 
| ECDHE\-ECDSA\-AES128\-GCM\-SHA256 | ♦ | ♦ | 
| ECDHE\-RSA\-AES128\-GCM\-SHA256 | ♦ | ♦ | 
| ECDHE\-ECDSA\-AES128\-SHA256 | ♦ | ♦ | 
| ECDHE\-RSA\-AES128\-SHA256 | ♦ | ♦ | 
| ECDHE\-ECDSA\-AES128\-SHA | ♦ |  | 
| ECDHE\-RSA\-AES128\-SHA | ♦ |  | 
| ECDHE\-ECDSA\-AES256\-GCM\-SHA384 | ♦ | ♦ | 
| ECDHE\-RSA\-AES256\-GCM\-SHA384 | ♦ | ♦ | 
| ECDHE\-ECDSA\-AES256\-SHA384 | ♦ | ♦ | 
| ECDHE\-RSA\-AES256\-SHA384 | ♦ | ♦ | 
| ECDHE\-RSA\-AES256\-SHA | ♦ |  | 
| ECDHE\-ECDSA\-AES256\-SHA | ♦ |  | 
| AES128\-GCM\-SHA256 | ♦ | ♦ | 
| AES128\-SHA256 | ♦ | ♦ | 
| AES128\-SHA | ♦ |  | 
| AES256\-GCM\-SHA384 | ♦ | ♦ | 
| AES256\-SHA256 | ♦ | ♦ | 
| AES256\-SHA | ♦ |  | 
| DES\-CBC3\-SHA | ♦ |  | 

## OpenSSL and RFC cipher names<a name="apigateway-secure-connections-openssl-rfc-cipher-names"></a>

OpenSSL and IETF RFC 5246, [The Transport Layer Security \(TLS\) Protocol Version 1\.2](https://tools.ietf.org/html/rfc5246), use different names for the same ciphers\. The following table maps the OpenSSL name to the RFC name for each cipher\. 


****  

| OpenSSL cipher name | RFC cipher name | 
| --- | --- | 
| ECDHE\-RSA\-AES128\-GCM\-SHA256 | TLS\_ECDHE\_RSA\_WITH\_AES\_128\_GCM\_SHA256 | 
| ECDHE\-RSA\-AES128\-SHA256 | TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA256  | 
| ECDHE\-RSA\-AES128\-SHA | TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA | 
| ECDHE\-RSA\-AES256\-GCM\-SHA384 | TLS\_ECDHE\_RSA\_WITH\_AES\_256\_GCM\_SHA384  | 
| ECDHE\-RSA\-AES256\-SHA384 | TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA384  | 
| ECDHE\-RSA\-AES256\-SHA | TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA | 
| AES128\-GCM\-SHA256 | TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256 | 
| AES256\-GCM\-SHA384 | TLS\_RSA\_WITH\_AES\_256\_GCM\_SHA384 | 
| AES128\-SHA256 | TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA256 | 
| AES256\-SHA | TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA | 
| AES128\-SHA | TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA | 
| DES\-CBC3\-SHA  | TLS\_RSA\_WITH\_3DES\_EDE\_CBC\_SHA  | 
| RC4\-MD5 | TLS\_RSA\_WITH\_RC4\_128\_MD5 | 