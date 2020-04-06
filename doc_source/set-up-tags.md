# Setting up tags for an API stage in API Gateway<a name="set-up-tags"></a>

In API Gateway, you can add a tag to an API stage, remove the tag from the stage, or view the tag\. To do this, you can use the API Gateway console, the AWS CLI/SDK, or the API Gateway REST API\.

A stage can also inherit tags from its parent REST API\. For more information, see [Tag inheritance in the Amazon API Gateway V1 API](apigateway-tagging-supported-resources.md#apigateway-tagging-inheritance)\.

For more information about tagging API Gateway resources, see [Tagging your API Gateway resources](apigateway-tagging.md)\.

**Topics**
+ [Set up tags for an API stage using the API Gateway console](#set-up-tags-using-console)
+ [Set up tags for an API stage using the API Gateway REST API](#set-up-tags-using-api)

## Set up tags for an API stage using the API Gateway console<a name="set-up-tags-using-console"></a>

The following procedure describes how to set up tags for an API stage\.

**To set up tags for an API stage by using the API Gateway console**

1. Sign in to the API Gateway console\.

1. Choose an existing API, or create a new API that includes resources, methods, and the corresponding integrations\.

1. Choose a stage or deploy the API to a new stage\.

1. In the **Stage Editor**, choose the **Configure Tags** button\. 

1. In the **Tag Editor**, choose **Add New Tag**\. Enter a tag key \(for example, `Department`\) in the **Key** field, and enter a tag value \(for example, `Sales`\) in the **Value** field\. Choose the checkmark icon to save the tag\.

1.  If needed, repeat step 5 to add more tags to the API stage\. The maximum number of tags per stage is 50\.

1.  To remove an existing tag from the stage, choose the trash bin icon next to the tag\.

1. Choose **Save Changes** to finish setting up the stage tags\.

   If the API has been deployed previously in the API Gateway console, you need to redeploy it for the changes to take effect\.

## Set up tags for an API stage using the API Gateway REST API<a name="set-up-tags-using-api"></a>

You can set up tags for an API stage using the API Gateway REST API by doing one of the following:
+ Call [https://docs.aws.amazon.com/apigateway/api-reference/link-relation/tags-tag/](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/tags-tag/) to tag an API stage\.
+  Call [https://docs.aws.amazon.com/apigateway/api-reference/link-relation/tags-untag/](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/tags-untag/) to delete one or more tags from an API stage\.
+ Call [https://docs.aws.amazon.com/apigateway/api-reference/link-relation/stage-create/](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/stage-create/) to add one or more tags to an API stage that you're creating\.

You can also call [https://docs.aws.amazon.com/apigateway/api-reference/link-relation/tags-get/](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/tags-get/) to describe tags in an API stage\.

### Tag an API stage<a name="tag-a-stage-using-api"></a>

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

### Untag an API stage<a name="untag-a-stage-using-api"></a>

 To remove the `Department` tag from the stage, call [https://docs.aws.amazon.com/apigateway/api-reference/link-relation/tags-untag/](https://docs.aws.amazon.com/apigateway/api-reference/link-relation/tags-untag/): 

```
DELETE /tags/arn%3Aaws%3Aapigateway%3Aus-east-1%3A%3A%2Frestapis%2Fm5zr3vnks7%2Fstages%2Ftest?tagKeys=Department
Host: apigateway.us-east-1.amazonaws.com
Authorization: ...
```

 To remove more than one tag, use a comma\-separated list of tag keys in the query expression—for example, `?tagKeys=Department,Division,…`\. 

### Describe tags for an API stage<a name="get-tags-using-api"></a>

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