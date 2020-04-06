# Getting certificates ready in AWS Certificate Manager<a name="how-to-custom-domains-prerequisites"></a>

Before setting up a custom domain name for an API, you must have an SSL/TLS certificate ready in AWS Certificate Manager\. The following steps describe how to get this done\. For more information, see the [AWS Certificate Manager User Guide](https://docs.aws.amazon.com/acm/latest/userguide/)\.

**Note**  
To use an ACM certificate with an API Gateway edge\-optimized custom domain name, you must request or import the certificate in the US East \(N\. Virginia\) \(`us-east-1`\) Region\. For an API Gateway Regional custom domain name, you must request or import the certificate in the same Region as your API\. The certificate must be signed by a publicly trusted Certificate Authority and cover the custom domain name\.

First, register your internet domain, for example, `example.com`\. You can use either [Amazon Route 53](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/) or a third\-party accredited domain registrar\. For a list of such registrars, see [Accredited Registrar Directory](http://www.internic.net/regist.html) at the ICANN website\. 

To create in or import into ACM an SSL/TLS certificate for a domain name, do one of the following: 

**To request a certificate provided by ACM for a domain name**

1. Sign in to the [AWS Certificate Manager console](https://console.aws.amazon.com/acm)\.

1. Choose **Request a certificate**\.

1. Enter a custom domain name for your API, for example, `api.example.com`, in **Domain name**\.

1. Optionally, choose **Add another name to this certificate**\.

1. Choose **Review and request**\.

1. Choose **Confirm and request**\.

1. For a valid request, a registered owner of the internet domain must consent to the request before ACM issues the certificate\.

**To import into ACM a certificate for a domain name**

1.  Get a PEM\-encoded SSL/TLS certificate for your custom domain name from a certificate authority\. For a partial list of such CAs, see the [Mozilla Included CA List](https://ccadb-public.secure.force.com/mozilla/IncludedCACertificateReport)  

   1. Generate a private key for the certificate and save the output to a file, using the [OpenSSL](http://www.openssl.org) toolkit at the OpenSSL website:

      ```
      openssl genrsa -out private-key-file 2048
      ```
**Note**  
Amazon API Gateway leverages Amazon CloudFront to support certificates for custom domain names\. As such, the requirements and constraints of a custom domain name SSL/TLS certificate are dictated by [CloudFront](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/using-https.html#cnames-and-https-requirements)\. For example, the maximum size of the public key is 2048 and the private key size can be 1024, 2048, and 4096\. The public key size is determined by the certificate authority you use\. Ask your certificate authority to return keys of a size different from the default length\. For more information, see [Secure access to your objects](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/SecureConnections.html) and [Create signed URLs and signed cookies](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-trusted-signers.html)\.

   1. Generate a certificate signing request \(CSR\) with the previously generated private key, using OpenSSL:

      ```
      openssl req -new -sha256 -key private-key-file -out CSR-file
      ```

   1. Submit the CSR to the certificate authority and save the resulting certificate\.

   1. Download the certificate chain from the certificate authority\.
**Note**  
 If you obtain the private key in another way and the key is encrypted, you can use the following command to decrypt the key before submitting it to API Gateway for setting up a custom domain name\.   

   ```
   openssl pkcs8 -topk8 -inform pem -in MyEncryptedKey.pem -outform pem -nocrypt -out MyDecryptedKey.pem
   ```

1. Upload the certificate to AWS Certificate Manager:

   1. Sign in to the [AWS Certificate Manager console](https://console.aws.amazon.com/acm)\.

   1. Choose **Import a certificate**\.

   1. For **Certificate body**, enter or paste the body of the PEM\-formatted server certificate from your certificate authority\. The following shows an abbreviated example of such a certificate\.

      ```
      -----BEGIN CERTIFICATE-----
      EXAMPLECA+KgAwIBAgIQJ1XxJ8Pl++gOfQtj0IBoqDANBgkqhkiG9w0BAQUFADBB
      ...
      az8Cg1aicxLBQ7EaWIhhgEXAMPLE
      -----END CERTIFICATE-----
      ```

   1. For **Certificate private key**, enter or paste your PEM\-formatted certificate's private key\. The following shows an abbreviated example of such a key\. 

      ```
      -----BEGIN RSA PRIVATE KEY-----
      EXAMPLEBAAKCAQEA2Qb3LDHD7StY7Wj6U2/opV6Xu37qUCCkeDWhwpZMYJ9/nETO
      ...
      1qGvJ3u04vdnzaYN5WoyN5LFckrlA71+CszD1CGSqbVDWEXAMPLE
      -----END RSA PRIVATE KEY-----
      ```

   1. For **Certificate chain**, enter or paste the PEM\-formatted intermediate certificates and, optionally, the root certificate, one after the other without any blank lines\. If you include the root certificate, your certificate chain must start with intermediate certificates and end with the root certificate\. Use the intermediate certificates provided by your certificate authority\. Do not include any intermediaries that are not in the chain of trust path\. The following shows an abbreviated example\. 

      ```
      -----BEGIN CERTIFICATE-----
      EXAMPLECA4ugAwIBAgIQWrYdrB5NogYUx1U9Pamy3DANBgkqhkiG9w0BAQUFADCB
      ...
      8/ifBlIK3se2e4/hEfcEejX/arxbx1BJCHBvlEPNnsdw8EXAMPLE
      -----END CERTIFICATE-----
      ```

      Here is another example\.

      ```
      -----BEGIN CERTIFICATE-----
      Intermediate certificate 2
      -----END CERTIFICATE-----
      -----BEGIN CERTIFICATE-----
      Intermediate certificate 1
      -----END CERTIFICATE-----
      -----BEGIN CERTIFICATE-----
      Optional: Root certificate
      -----END CERTIFICATE-----
      ```

   1. Choose **Review and import**\.

After the certificate is successfully created or imported, make note of the certificate ARN\. You need it when setting up the custom domain name\.