# Configuring AWS X\-Ray sampling rules for API Gateway APIs<a name="apigateway-configuring-xray-sampling-rules"></a>

You can use AWS X\-Ray console or SDK to configure sampling rules for your Amazon API Gateway API\. A sampling rule specifies which requests X\-Ray should record for your API\. By customizing sampling rules, you can control the amount of data that you record, and modify sampling behavior on the fly without modifying or redeploying your code\.

Before you specify your X\-Ray sampling rules, read the following topics in the X\-Ray Developer Guide:
+ [Configuring Sampling Rules in the AWS X\-Ray Console](https://docs.aws.amazon.com/xray/latest/devguide/xray-console-sampling.html)
+ [Using Sampling Rules with the X\-Ray API](https://docs.aws.amazon.com/xray/latest/devguide/xray-api-sampling.html)

**Topics**
+ [X\-Ray sampling rule option values for API Gateway APIs](#apigateway-xray-sampling-rule-options)
+ [X\-Ray sampling rule examples](#apigateway-xray-sampling-rules-examples)

## X\-Ray sampling rule option values for API Gateway APIs<a name="apigateway-xray-sampling-rule-options"></a>

The following X\-Ray sampling options are relevant for API Gateway\. String values can use wildcards to match a single character \(?\) or zero or more characters \(\*\)\. For more details, including a detailed explanation of how the **Reservoir** and **Rate** settings are used, [Configuring Sampling Rules in the AWS X\-Ray Console](https://docs.aws.amazon.com/xray/latest/devguide/xray-console-sampling.html)\.
+ **Rule name** \(string\) — A unique name for the rule\.
+ **Priority** \(integer between 1 and 9999\) — The priority of the sampling rule\. Services evaluate rules in ascending order of priority, and make a sampling decision with the first rule that matches\.
+ **Reservoir** \(nonnegative integer\) — A fixed number of matching requests to instrument per second, before applying the fixed rate\. The reservoir is not used directly by services, but applies to all services using the rule collectively\.
+ **Rate** \(number between 0 and 100\) — The percentage of matching requests to instrument, after the reservoir is exhausted\.
+ **Service name** \(string\) — API stage name, in the form ***\{api\-name\}*/*\{stage\-name\}***\. For example, if you were to deploy the [PetStore](api-gateway-create-api-from-example.md) sample API to a stage named `test`, the **Service name** value to specify in your sampling rule would be **pets/test**\.
+ **Service type** \(string\) — For an API Gateway API, either **AWS::ApiGateway::Stage** or **AWS::ApiGateway::\*** can be specified\.
+ **Host** \(string\) — The hostname from the HTTP host header\. Set this to **\*** to match against all hostnames\. Or you can specify a full or partial hostname to match, for example, **api\.example\.com** or **\*\.example\.com**\.
+ **Resource ARN** \(string\) — The ARN of the API stage, in the format **arn:aws:execute\-api:*\{region\}*:*\{account\-id\}*:*\{api\-id\}*/*\{stage\-name\}***, for example, **arn:aws:execute\-api:us\-east\-1:123456789012:qsxrty/test**\.

  The stage name can be obtained from the console or the API Gateway CLI or API\. For more information about ARN formats, see the [Amazon Web Services General Reference](https://docs.aws.amazon.com/general/latest/gr/)\.
+ **HTTP method** \(string\) — The method to be sampled, for example, **GET**\.
+ **URL path** \(string\) — This option is not supported for API Gateway\.
+ \(optional\) **Attributes** \(key and value\) — Headers from the original HTTP request, for example, **Connection**, **Content\-Length**, or **Content\-Type**\. Each attribute value can be up to 32 characters long\.

## X\-Ray sampling rule examples<a name="apigateway-xray-sampling-rules-examples"></a>

**Sampling rule example \#1**

This rule samples all `GET` requests for the `testxray` API at the `test` stage\.
+ **Rule name — ****test\-sampling**
+ **Priority — ****17**
+ **Reservoir size — ****10**
+ **Fixed rate — ****10**
+ **Service name — ****testxray/test**
+ **Service type — ****AWS::ApiGateway::Stage**
+ **HTTP method — ****GET**
+ **Resource ARN — ****\***
+ **Host — ****\***

**Sampling rule example \#2**

This rule samples all requests for the `testxray` API at the `prod` stage\.
+ **Rule name — ****prod\-sampling**
+ **Priority — ****478**
+ **Reservoir size — ****1**
+ **Fixed rate — ****60**
+ **Service name — ****testxray/prod**
+ **Service type — ****AWS::ApiGateway::Stage**
+ **HTTP method — ****\***
+ **Resource ARN — ****\***
+ **Host — ****\***
+ **Attributes** — **\{\}**