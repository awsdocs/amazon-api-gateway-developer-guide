# AWS Conditions that can be used in API Gateway Resource Policies<a name="apigateway-resource-policies-aws-condition-keys"></a>

The following table contains the complete list of AWS condition keys that can be used in resource policies for APIs in API Gateway for each authorization type\.


**Table of Condition Keys**  

| Condition Keys | Criteria | Needs `AuthN`? | Authorization Type | 
| --- | --- | --- | --- | 
| aws:CurrentTime | None | No | All | 
| aws:EpochTime | None | No | All | 
| aws:TokenIssueTime | Key is present only in requests that are signed using temporary security credentials\. | Yes | IAM | 
| aws:MultiFactorAuthPresent | Key is present only in requests that are signed using temporary security credentials\. | Yes | IAM | 
| aws:MultiFactorAuthAge | Key is present only if MFA is present in the requests\. | Yes | IAM | 
| aws:PrincipalType | None | Yes | IAM | 
| aws:Referer | Key is present only if the value is provided by the caller in the HTTP header\. | No | All | 
| aws:SecureTransport | None | No | All | 
| aws:SourceArn | None | No | All | 
| aws:SourceIp | None | No | All | 
| aws:UserAgent | Key is present only if the value is provided by the caller in the HTTP header\. | No | All | 
| aws:userid | None | Yes | IAM | 
| aws:username | None | Yes | IAM | 