# AWS condition keys that can be used in API Gateway resource policies<a name="apigateway-resource-policies-aws-condition-keys"></a>

The following table contains AWS condition keys that can be used in resource policies for APIs in API Gateway for each authorization type\.

For more information about AWS condition keys, see [AWS Global Condition Context Keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html)\.


**Table of condition keys**  

| Condition keys | Criteria | Needs `AuthN`? | Authorization type | 
| --- | --- | --- | --- | 
| aws:CurrentTime | None | No | All | 
| aws:EpochTime | None | No | All | 
| aws:TokenIssueTime | Key is present only in requests that are signed using temporary security credentials\. | Yes | IAM | 
| aws:MultiFactorAuthPresent | Key is present only in requests that are signed using temporary security credentials\. | Yes | IAM | 
| aws:MultiFactorAuthAge | Key is present only if MFA is present in the requests\. | Yes | IAM | 
| aws:PrincipalAccount | None | Yes | IAM | 
| aws:PrincipalArn | None | Yes | IAM | 
| aws:PrincipalOrgID | This key is included in the request context only if the principal is a member of an organization\. | Yes | IAM | 
| aws:PrincipalOrgPaths | This key is included in the request context only if the principal is a member of an organization\. | Yes | IAM | 
| aws:PrincipalTag | This key is included in the request context if the principal is using an IAM user with attached tags\. It is included for a principal using an IAM role with attached tags or session tags\. | Yes | IAM | 
| aws:PrincipalType | None | Yes | IAM | 
| aws:Referer | Key is present only if the value is provided by the caller in the HTTP header\. | No | All | 
| aws:SecureTransport | None | No | All | 
| aws:SourceIp | None | No | All | 
| aws:SourceVpc | This key can be used only for private APIs\. | No | All | 
| aws:SourceVpce | This key can be used only for private APIs\. | No | All | 
| aws:UserAgent | Key is present only if the value is provided by the caller in the HTTP header\. | No | All | 
| aws:userid | None | Yes | IAM | 
| aws:username | None | Yes | IAM | 