# Interactions Between API Gateway Resource Policies and IAM Policies<a name="apigateway-resource-policies-iam-policies-interaction"></a>

The following tables list the resulting behavior when access to an API Gateway API is controlled by both an IAM policy and an API Gateway resource policy\.

For more information about how an AWS service decides whether a given request should be allowed or denied, see [Determining Whether a Request is Allowed or Denied](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic.html#policy-eval-denyallow)\.

**Note**  
"Implicit deny" is the same thing as "Deny by default\."  
"Implicit deny" is different from "Explict deny\." For more information, see [The Difference Between Denying by Default and Explicit Deny](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic.html#AccessPolicyLanguage_Interplay)\.


**Account A Calls API Owned by Account A**  

| IAM User Policy | API Gateway Resource Policy | Resulting Behavior | 
| --- | --- | --- | 
| Allow | Allow | Allow | 
| Allow | Neither Allow nor Deny | Allow | 
| Allow | Deny | Explicit Deny | 
| Neither Allow nor Deny | Allow | Allow | 
| Neither Allow nor Deny | Neither Allow nor Deny | Implicit Deny | 
| Neither Allow nor Deny | Deny | Explicit Deny | 
| Deny | Allow | Explicit Deny | 
| Deny | Neither Allow nor Deny | Explicit Deny | 
| Deny | Deny | Explicit Deny | 

The following table shows the behavior when the IAM or resource policy is silent is that the access is denied \(cross account access requires that both the resource policy and the IAM policy explicitly grant access\.


**Account B Calls API Owned by Account A**  

| IAM User Policy | API Gateway Resource Policy | Resulting Behavior | 
| --- | --- | --- | 
| Allow | Allow | Allow | 
| Allow | Neither Allow nor Deny | Implicit Deny | 
| Allow | Deny | Explicit Deny | 
| Neither Allow nor Deny | Allow | Implicit Deny | 
| Neither Allow nor Deny | Neither Allow nor Deny | Implicit Deny | 
| Neither Allow nor Deny | Deny | Explicit Deny | 
| Deny | Allow | Explicit Deny | 
| Deny | Neither Allow nor Deny | Explicit Deny | 
| Deny | Deny | Explicit Deny | 