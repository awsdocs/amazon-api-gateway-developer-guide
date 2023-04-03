# Configuring mutual TLS authentication for a REST API<a name="rest-api-mutual-tls"></a>

Mutual TLS authentication requires two\-way authentication between the client and the server\. With mutual TLS, clients must present X\.509 certificates to verify their identity to access your API\. Mutual TLS is a common requirement for Internet of Things \(IoT\) and business\-to\-business applications\. 

You can use mutual TLS along with other [authorization and authentication operations](apigateway-control-access-to-api.md) that API Gateway supports\. API Gateway forwards the certificates that clients provide to Lambda authorizers and to backend integrations\.

**Important**  
By default, clients can invoke your API by using the `execute-api` endpoint that API Gateway generates for your API\. To ensure that clients can access your API only by using a custom domain name with mutual TLS, disable the default `execute-api` endpoint\. To learn more, see [Disabling the default endpoint for a REST API](rest-api-disable-default-endpoint.md)\. 

**Topics**
+ [Prerequisites for mutual TLS](#rest-api-mutual-tls-prerequisites)
+ [Configuring mutual TLS for a custom domain name](#rest-api-mutual-tls-configure)
+ [Invoke an API by using a custom domain name that requires mutual TLS](#rest-api-mutual-tls-invoke)
+ [Updating your truststore](#rest-api-mutual-tls-update-truststore)
+ [Disable mutual TLS](#rest-api-mutual-tls-disable)
+ [Troubleshooting certificate warnings](#rest-api-mutual-tls-troubleshooting)
+ [Troubleshooting domain name conflicts](#w151aac15c21b7c23)
+ [Troubleshooting domain name status messages](#w151aac15c21b7c25)

## Prerequisites for mutual TLS<a name="rest-api-mutual-tls-prerequisites"></a>

To configure mutual TLS you need:
+ A custom domain name
+ At least one certificate configured in AWS Certificate Manager for your custom domain name
+ A truststore configured and uploaded to Amazon S3

### Custom domain names<a name="rest-api-mutual-tls-custom-domain-name"></a>

 To enable mutual TLS for a REST API, you must configure a custom domain name for your API\. You can enable mutual TLS for a custom domain name, and then provide the custom domain name to clients\. To access an API by using a custom domain name that has mutual TLS enabled, clients must present certificates that you trust in API requests\. You can find more information at [Setting up custom domain names for REST APIs](how-to-custom-domains.md)\.

### <a name="rest-api-mutual-tls-preparing-certificates"></a>

#### Using AWS Certificate Manager issued certificates<a name="rest-api-mutual-tls-using-acm-issued-certs"></a>

You can request a publicly trusted certificate directly from ACM or import public or self\-signed certificates\. To setup a certificate in ACM, go to [ACM](https://console.aws.amazon.com/acm/)\. If you would like to import a certificate, continue reading in the following section\.

#### Using an imported or AWS Certificate Manager Private Certificate Authority certificate<a name="rest-api-mutual-tls-non-acm-certs"></a>

To use a certificate imported into ACM or a certificate from AWS Certificate Manager Private Certificate Authority with mutual TLS, API Gateway needs an `ownershipVerificationCertificate` issued by ACM\. This ownership certificate is only used to verify that you have permissions to use the domain name\. It is not used for the TLS handshake\. If you don't already have a `ownershipVerificationCertificate`, go to [https://console\.aws\.amazon\.com/acm/](https://console.aws.amazon.com/acm/) to set one up\.

You will need to keep this certificate valid for the lifetime of your domain name\. If a certificate expires and auto\-renew fails, all updates to the domain name will be locked\. You will need to update the `ownershipVerificationCertificateArn` with a valid `ownershipVerificationCertificate` before you can make any other changes\. The `ownershipVerificationCertificate` cannot be used as a server certificate for another mutual TLS domain in API Gateway\. If a certificate is directly re\-imported into ACM, the issuer must stay the same\.

### Configuring your truststore<a name="rest-api-mutual-tls-create-trust-store"></a>

Truststores are text files with a `.pem` file extension\. They are a trusted list of certificates from Certificate Authorities\. To use mutual TLS, create a truststore of X\.509 certificates that you trust to access your API\.

You must include the complete chain of trust, starting from the issuing CA certificate, up to the root CA certificate, in your truststore\. API Gateway accepts client certificates issued by any CA present in the chain of trust\. The certificates can be from public or private certificate authorities\. Certificates can have a maximum chain length of four\. You can also provide self\-signed certificates\. The following hashing algorithms are supported in the truststore:
+ SHA\-256 or stronger
+ RSA\-2048 or stronger
+ ECDSA\-256 or stronger

API Gateway validates a number of certificate properties\. You can use Lambda authorizers to perform additional checks when a client invokes an API, including checking whether a certificate has been revoked\. API Gateway validates the following properties:


| Validation | Description | 
| --- | --- | 
|  X\.509 syntax  |  The certificate must meet X\.509 syntax requirements\.  | 
|  Integrity  |  The certificate's content must not have been altered from that signed by the certificate authority from the truststore\.  | 
|  Validity  |  The certificate's validity period must be current\.  | 
|  Name chaining / key chaining  |  The names and subjects of certificates must form an unbroken chain\. Certificates can have a maximum chain length of four\.  | 

### Upload the truststore to an Amazon S3 bucket in a single file<a name="w151aac15c21b7c11c13"></a>

The following is an example of what a \.pem file might look like\.

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

The following AWS CLI command uploads `certificates.pem` to your Amazon S3 bucket\.

```
aws s3 cp certificates.pem s3://bucket-name
```

## Configuring mutual TLS for a custom domain name<a name="rest-api-mutual-tls-configure"></a>

To configure mutual TLS for a REST API, you must use a Regional custom domain name for your API, with a minimum TLS version of 1\.2\. To learn more about creating and configuring a custom domain name, see [Setting up a regional custom domain name in API Gateway](apigateway-regional-api-custom-domain-create.md)\.

**Note**  
Mutual TLS isn't supported for private APIs\.

After you've uploaded your truststore to Amazon S3, you can configure your custom domain name to use mutual TLS\. Paste the following \(slashes included\) into a terminal:

```
aws apigateway create-domain-name --region us-east-2 \
    --domain-name api.example.com \
    --regional-certificate-arn arn:aws:acm:us-east-2:123456789012:certificate/123456789012-1234-1234-1234-12345678 \
    --endpoint-configuration types=REGIONAL \
    --security-policy TLS_1_2 \
    --mutual-tls-authentication truststoreUri=s3://bucket-name/key-name
```

After you create the domain name, you must configure DNS records and basepath mappings for API operations\. To learn more, see [Setting up a regional custom domain name in API Gateway](apigateway-regional-api-custom-domain-create.md)\.

## Invoke an API by using a custom domain name that requires mutual TLS<a name="rest-api-mutual-tls-invoke"></a>

To invoke an API with mutual TLS enabled, clients must present a trusted certificate in the API request\. When a client attempts to invoke your API, API Gateway looks for the client certificate's issuer in your truststore\. For API Gateway to proceed with the request, the certificate's issuer and the complete chain of trust up to the root CA certificate must be in your truststore\.

The following example `curl` command sends a request to `api.example.com,` that includes `my-cert.pem` in the request\. `my-key.key` is the private key for the certificate\.

```
curl -v --key ./my-key.key --cert ./my-cert.pem api.example.com
```

Your API is invoked only if your truststore trusts the certificate\. The following conditions will cause API Gateway to fail the TLS handshake and deny the request with a `403` status code\. If your certificate:
+ isn't trusted
+ is expired
+ doesn't use a supported algorithm

**Note**  
API Gateway doesn't verify if a certificate has been revoked\.

## Updating your truststore<a name="rest-api-mutual-tls-update-truststore"></a>

To update the certificates in your truststore, upload a new certificate bundle to Amazon S3\. Then, you can update your custom domain name to use the updated certificate\.

Use [Amazon S3 versioning](https://docs.aws.amazon.com/AmazonS3/latest/dev/Versioning.html) to maintain multiple versions of your truststore\. When you update your custom domain name to use a new truststore version, API Gateway returns warnings if certificates are invalid\.

API Gateway produces certificate warnings only when you update your domain name\. API Gateway doesn’t notify you if a previously uploaded certificate expires\.

The following AWS CLI command updates a custom domain name to use a new truststore version\.

```
aws apigateway update-domain-name \
    --domain-name api.example.com \
    --patch-operations op='replace',path='/mutualTlsAuthentication/truststoreVersion',value='abcdef123'
```

## Disable mutual TLS<a name="rest-api-mutual-tls-disable"></a>

To disable mutual TLS for a custom domain name, remove the truststore from your custom domain name, as shown in the following command\.

```
aws apigateway update-domain-name \
    --domain-name api.example.com \
    --patch-operations op='replace',path='/mutualTlsAuthentication/truststoreUri',value=''
```

## Troubleshooting certificate warnings<a name="rest-api-mutual-tls-troubleshooting"></a>

 When creating a custom domain name with mutual TLS, API Gateway returns warnings if certificates in the truststore are not valid\. This can also occur when updating a custom domain name to use a new truststore\. The warnings indicate the issue with the certificate and the subject of the certificate that produced the warning\. Mutual TLS is still enabled for your API, but some clients might not be able to access your API\.

You'll need to decode the certificates in your truststore in order to identify which certificate produced the warning\. You can use tools such as `openssl` to decode the certificates and identify their subjects\.

The following command displays the contents of a certificate, including its subject:

```
openssl x509 -in certificate.crt -text -noout
```

Update or remove the certificates that produced warnings, and then upload a new truststore to Amazon S3\. After uploading the new truststore, update your custom domain name to use the new truststore\.

## Troubleshooting domain name conflicts<a name="w151aac15c21b7c23"></a>

The error `"The certificate subject <certSubject> conflicts with an existing certificate from a different issuer."` means multiple Certificate Authorities have issued a certificate for this domain\. For each subject in the certificate, there can only be one issuer in API Gateway for mutual TLS domains\. You will need to get all of your certificates for that subject through a single issuer\. If the problem is with a certificate you don't have control of but you can prove ownership of the domain name, [contact AWS Support](https://console.aws.amazon.com/support/cases#/create) to open a ticket\.

## Troubleshooting domain name status messages<a name="w151aac15c21b7c25"></a>

`PENDING_CERTIFICATE_REIMPORT`: This means you reimported a certificate to ACM and it failed validation because the new certificate has a SAN \(subject alternative name\) that is not covered by the `ownershipVerificationCertificate` or the subject or SANs in the certificate don't cover the domain name\. Something might be configured incorrectly or an invalid certificate was imported\. You need to reimport a valid certificate into ACM\. For more information about validation see [Validating domain ownership](https://docs.aws.amazon.com/acm/latest/userguide/domain-ownership-validation.html)\.

`PENDING_OWNERSHIP_VERIFICATION`: This means your previously verified certificate has expired and ACM was unable to auto\-renew it\. You will need to renew the certificate or request a new certificate\. More information about certificate renewal can be found at [ACM's troubleshooting managed certificate renewal](https://docs.aws.amazon.com/acm/latest/userguide/troubleshooting-renewal.html) guide\.