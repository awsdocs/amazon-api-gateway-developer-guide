# Configuring mutual TLS authentication for an HTTP API<a name="http-api-mutual-tls"></a>

Mutual TLS authentication requires two\-way authentication between the client and the server\. With mutual TLS, clients must present X\.509 certificates to verify their identity to access your API\. Mutual TLS is a common requirement for Internet of Things \(IOT\) and business\-to\-business applications\. 

To enable mutual TLS for an HTTP API, you must configure a custom domain name for your API\. You can enable mutual TLS for a custom domain name, and then provide the custom domain name to clients\. To access an API by using a custom domain name that has mutual TLS enabled, clients must present certificates that you trust in API requests\.

You can use mutual TLS along with other [authorization and authentication methods](http-api-access-control.md) that API Gateway supports\. API Gateway forwards the certificates that clients provide to Lambda authorizers and to backend Lambda integrations\.

**Important**  
By default, clients can invoke your API by using the `execute-api` endpoint that API Gateway generates for your API\. To ensure that clients can access your API only by using a custom domain name with mutual TLS, disable the default `execute-api` endpoint\. To learn more, see [Disabling the default endpoint for an HTTP API](http-api-disable-default-endpoint.md)\. 

## Configuring mutual TLS for a custom domain name<a name="http-api-mutual-tls-configure"></a>

To use mutual TLS, create a truststore of X\.509 certificates that you trust to access your API\. The certificates can be from public or private certificate authorities\. Certificates can have a maximum chain length of four\. You can also provide self\-signed certificates\. The following are supported:
+ SHA\-256 or stronger
+ RSA\-2048 or stronger
+ ECDSA\-256 or stronger

API Gateway validates a number of certificate properties\. You can use Lambda authorizers to perform additional checks when a client invokes an API, including checking if a certificate has been revoked\. API Gateway validates the following certificate properties:


| Validation | Description | 
| --- | --- | 
|  X\.509 syntax  |  The certificate must meet X\.509 syntax requirements\.  | 
|  Integrity  |  The certificate's content must not have been altered from that signed by the certificate authority from the truststore\.  | 
|  Validity  |  The certificate's validity period must be current\.  | 
|  Name chaining / key chaining  |  The names and subjects of certificates must form an unbroken chain\. Certificates can have a maximum chain length of four\.  | 

Upload the truststore to an Amazon S3 bucket in a single file\.

**Example certificates\.pem**  

```
-----BEGIN CERTIFICATE-----
<Certificate contents>
-----END CERTIFICATE-----
-----BEGIN CERTIFICATE-----
<Certificate contents>
-----END CERTIFICATE-----
-----BEGIN CERTIFICATE-----
<Certificate contents>
-----END CERTIFICATE-----
...
```

The following AWS CLI command uploads `certificates.pem` to Amazon S3\.

```
aws s3 cp certificates.pem s3://bucket-name
```

After you've uploaded your truststore to Amazon S3, you can configure your custom domain name to use mutual TLS\.

When you create a custom domain name that has mutual TLS enabled, or update a custom domain to use a new truststore, API Gateway returns warnings if certificates in the truststore are invalid\. Mutual TLS is still enabled, but some clients might not be able to access your API\. To resolve warnings, upload a new truststore to S3, and then update your domain name to use the new version\. For more information, see [Troubleshooting certificate warnings](#http-api-mutual-tls-troubleshooting)\.

**Note**  
To enable mutual TLS, your domain name must use a publicly trusted server certificate issued by AWS Certificate Manager\.

```
aws apigatewayv2 create-domain-name \
    --domain-name api.example.com \
    --domain-name-configurations CertificateArn=arn:aws:acm:us-west-2:123456789012:certificate/123456789012-1234-1234-1234-12345678 \
    --mutual-tls-authentication TruststoreUri=s3://bucket-name/key-name
```

After you create the domain name, you must configure DNS records and basepath mappings for APIs\. To learn more, see [Setting up a regional custom domain name in API Gateway](apigateway-regional-api-custom-domain-create.md)\.

## Invoke an API by using a custom domain name that requires mutual TLS<a name="http-api-mutual-tls-invoke"></a>

To invoke an API with mutual TLS enabled, clients must present a trusted certificate in the API request\. The following example `curl` command sends a request to `api.example.com,` that includes `my-cert.pem` in the request\. `my-key.key` is the private key for the certificate\.

```
curl -v --key ./my-key.key --cert ./my-cert.pem api.example.com
```

Your API is invoked only if your truststore trusts the certificate\. If the certificate isn't trusted, is expired, or doesn't use a supported algorithm, the request is denied with a `403` status code\.

**Note**  
API Gateway doesn't verify if a certificate has been revoked\.

## Updating your truststore<a name="http-api-mutual-tls-update-truststore"></a>

To update the certificates in your truststore, upload a new certificate bundle to Amazon S3, and then update your custom domain name to use the new version\.

Use [Amazon S3 versioning](https://docs.aws.amazon.com/AmazonS3/latest/dev/Versioning.html) to maintain multiple versions of your truststore\. When you update your custom domain name to use a new truststore version, API Gateway returns warnings if certificates are invalid\.

**Note**  
API Gateway produces certificate warnings only when you update your domain name to use a new truststore\. API Gateway doesn’t notify you if a previously uploaded certificate expires\.

The following AWS CLI command updates a custom domain name to use a new truststore version\.

```
aws apigatewayv2 update-domain-name \
    --domain-name api.example.com \
    --mutual-tls-authentication truststoreVersion='abcdef123'
```

## Disable mutual TLS<a name="http-api-mutual-tls-disable"></a>

To disable mutual TLS for a custom domain name, remove the truststore from your custom domain name, as shown in the following command\.

```
aws apigatewayv2 update-domain-name \
    --domain-name api.example.com \
    --mutual-tls-authentication truststoreUri=''
```

## Troubleshooting certificate warnings<a name="http-api-mutual-tls-troubleshooting"></a>

When you create a custom domain name that has mutual TLS enabled, or you update your custom domain name to use a new truststore, API Gateway returns warnings if certificates are invalid\. The warnings indicate the issue with the certificate and the subject of the certificate that produced the warning\.

To identify the certificate that produced the warning, decode the certificates in your truststore\. You can use tools such as `openssl` to decode the certificates and identify their subjects\.

The following command displays the contents of a certificate, including its subject\.

```
openssl x509 -in certificate.crt -text -noout
```

Update or remove the certificates that produced warnings, and then upload a new truststore version to Amazon S3\. After uploading a new truststore to Amazon S3, update your custom domain name to use the new truststore\.