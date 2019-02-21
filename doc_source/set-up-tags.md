# Set up Tags for an API Stage in API Gateway<a name="set-up-tags"></a>

Tags are metadata that you assign to your AWS resources\. They are commonly used for tracking resource usage by custom\-defined categories, which provides a simple mechanism to separate distinct organizational units within a single AWS account\. A tag consists of a key\-value pair\. For more information on how to use tags, see [AWS Tagging Strategy](https://aws.amazon.com/answers/account-management/aws-tagging-strategies/)\.

 In API Gateway, you can assign tags to an API stage for managing cost allocation for request invocation and caching that are associated with the stage\. For example, when you add the tag `Department:Sales` to an API stage, it shows up in AWS Billing and Cost Management as a cost allocation tag\. After a tag is activated, you can use it to filter costs and usage by using [Cost Explorer](http://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/cost-explorer-what-is.html) in the [AWS Billing and Cost Management](http://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-what-is.html) console\. 

You can add a tag to an API stage, remove the tag from the stage, or view the tag\. To do this, you can use the API Gateway console, the AWS CLI/SDK, or the API Gateway REST API\.

**Topics**
+ [Set up Tags for an API Stage Using the API Gateway Console](#set-up-tags-using-console)
+ [Set up Tags for an API Stage Using the API Gateway REST API](#set-up-tags-using-api)
+ [Tag Restrictions](#api-gateway-tag-restrictions)

## Set up Tags for an API Stage Using the API Gateway Console<a name="set-up-tags-using-console"></a>

The following procedure describes how to set up tags for an API stage\.

**To set up tags for an API stage by using the API Gateway console**

1. Sign in to the API Gateway console\.

1. Choose an existing API, or create a new API that includes resources, methods, and the corresponding integrations\.

1. Choose a stage or deploy the API to a new stage\.

1. In the **Stage Editor**, choose the **Settings** tab\. 

1.  Under the **Tags** section, choose **Add Stage Tag**\. Type a tag key \(for example, `Department`\) in the **Key** column, and type a tag value \(for example, `Sales`\) in the **Value** column\. Choose the checkmark icon to save the tag\.

1.  If needed, repeat Step 5 to add more tags to the API stage\. The maximum number of tags per stage is 50\.

1.  To remove an existing tag from the stage, choose the trash bin icon next to the selected tag\.

1. Choose **Save Changes** to finish setting up the stage tags\.

   If the API has been deployed previously in the API Gateway console, you'll need to redeploy it for the changes to take effect\.

## Set up Tags for an API Stage Using the API Gateway REST API<a name="set-up-tags-using-api"></a>

You can set up tags for an API stage using the API Gateway REST API by doing one of the following:
+ Call [https://docs.aws.amazon.com/apigateway/api-reference/link-relation/tags-tag/](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/tags-tag/) to tag an API stage\.
+  Call [https://docs.aws.amazon.com/apigateway/api-reference/link-relation/tags-untag/](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/tags-untag/) to delete one or more tags from an API stage\.
+ Call [https://docs.aws.amazon.com/apigateway/api-reference/link-relation/stage-create/](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/stage-create/) to add one or more tags to an API stage that you're creating\.

You can also call [https://docs.aws.amazon.com/apigateway/api-reference/link-relation/tags-get/](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/tags-get/) to describe tags in an API stage\.

### Tag an API Stage<a name="tag-a-stage-using-api"></a>

After you deploy an API \(`m5zr3vnks7`\) to a stage \(`test`\), tag the stage by calling [https://docs.aws.amazon.com/apigateway/api-reference/link-relation/tags-tag/](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/tags-tag/)\. The required stage Amazon Resource Name \(ARN\) \(`arn:aws:apigateway:us-east-1::/restapis/m5zr3vnks7/stages/test`\) must be URL encoded \(`arn%3Aaws%3Aapigateway%3Aus-east-1%3A%3A%2Frestapis%2Fm5zr3vnks7%2Fstages%2Ftest`\)\. 

```
PUT /tags/arn%3Aaws%3Aapigateway%3Aus-east-1%3A%3A%2Frestapis%2Fm5zr3vnks7%2Fstages%2Ftest

{
  "tags" : {
    "Department" : "Sales"
  }
}
```

 You can also use the previous request to update an existing tag to a new value\. 

You can add tags to a stage when calling [https://docs.aws.amazon.com/apigateway/api-reference/link-relation/stage-create/](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/stage-create/) to create the stage:

```
POST /restapis/<restapi_id>/stages

{
  "stageName" : "test",
  "deploymentId" : "adr134",
  "description" : "test deployment",
  "cacheClusterEnabled" : "true",
  "cacheClusterSize" : "500",
  "variables" : {
    "sv1" : "val1"
  },
  "documentationVersion" : "test",

  "tags" : {
    "Department" : "Sales",
    "Division" : "Retail"
  }
}
```

### Untag an API Stage<a name="untag-a-stage-using-api"></a>

 To remove the `Department` tag from the stage, call [https://docs.aws.amazon.com/apigateway/api-reference/link-relation/tags-untag/](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/tags-untag/): 

```
DELETE /tags/arn%3Aaws%3Aapigateway%3Aus-east-1%3A%3A%2Frestapis%2Fm5zr3vnks7%2Fstages%2Ftest?tagKeys=Department
Host: apigateway.us-east-1.amazonaws.com
Authorization: ...
```

 To remove more than one tag, use a comma\-separated list of tag keys in the query expression—for example,`?tagKeys=Department,Division,…`\. 

### Describe Tags for an API Stage<a name="get-tags-using-api"></a>

To describe existing tags on a given stage, call [https://docs.aws.amazon.com/apigateway/api-reference/link-relation/tags-get/](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/tags-get/):

```
GET /tags/arn%3Aaws%3Aapigateway%3Aus-east-1%3A%3A%2Frestapis%2Fm5zr3vnks7%2Fstages%2Ftags
Host: apigateway.us-east-1.amazonaws.com
Authorization: ...
```

The successful response is similar to the following:

```
200 OK

{
    "_links": {
        "curies": {
            "href": "http://docs.aws.amazon.com/apigateway/latest/developerguide/restapi-tags-{rel}.html",
            "name": "tags",
            "templated": true
        },
        "tags:tag": {
            "href": "/tags/arn%3Aaws%3Aapigateway%3Aus-east-1%3A%3A%2Frestapis%2Fm5zr3vnks7%2Fstages%2Ftags"
        },
        "tags:untag": {
            "href": "/tags/arn%3Aaws%3Aapigateway%3Aus-east-1%3A%3A%2Frestapis%2Fm5zr3vnks7%2Fstages%2Ftags{?tagKeys}",
            "templated": true
        }
    },
    "tags": {
        "Department": "Sales"
    }
}
```

## Tag Restrictions<a name="api-gateway-tag-restrictions"></a>

The following restrictions apply to tags for API Gateway resources:
+ Tags are applicable to the [https://docs.aws.amazon.com/apigateway/api-reference/resource/stage/](https://docs.aws.amazon.com/apigateway/api-reference/resource/stage/) resource only\.
+ The maximum number of tags per stage is 50\.
+ The maximum tag key length is 128 Unicode characters in UTF\-8\.
+ The maximum tag value length is 256 Unicode characters in UTF\-8\.
+ Tag keys and values are case sensitive\.
+ The valid character set is \[a\-zA\-Z\+\-=\.\_:/\] for tag keys and values\.
+ Tag keys and values can't start with `aws:`\.