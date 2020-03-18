# Publishing HTTP APIs for Customers to Invoke<a name="http-api-publish"></a>

You can use stages and custom domain names to publish your API for clients to invoke\.

An API stage is a logical reference to a lifecycle state of your API \(for example, `dev`, `prod`, `beta`, or `v2`\)\. Each stage is a named reference to a deployment of the API and is made available for client applications to call\. You can configure different integrations and settings for each stage of an API\.

You can use custom domain names to provide a simpler, more intuitive URL for clients to invoke your API than the default URL, `https://api-id.execute-api.region.amazonaws.com/stage`\.

**Topics**
+ [Working with Stages for HTTP APIs](http-api-stages.md)
+ [Setting Up Custom Domain Names for HTTP APIs](http-api-custom-domain-names.md)