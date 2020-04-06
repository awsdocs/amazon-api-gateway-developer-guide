# Control access to API documentation<a name="api-gateway-documenting-api-content-provision-and-consumption"></a>

If you have a dedicated documentation team to write and edit your API documentation, you can configure separate access permissions for your developers \(for API development\) and for your writers or editors \(for content development\)\. This is especially appropriate when a third\-party vendor is involved in creating the documentation for you\. 

 To grant your documentation team the access to create, update, and publish your API documentation, you can assign the documentation team an IAM role with the following IAM policy, where *account\_id* is the AWS account ID of your documentation team\. 

```
{
  "Version": "2012-10-17",
  "Statement": [

    {
      "Sid": "StmtDocPartsAddEditViewDelete",
      "Effect": "Allow",
      "Action": [
        "apigateway:GET",
        "apigateway:PUT",
        "apigateway:POST",
        "apigateway:PATCH",
        "apigateway:DELETE"
      ],
      "Resource": [
        "arn:aws:apigateway::account_id:/restapis/*/documentation/*"
      ]
    }
  ]
}
```

 For information on setting permissions to access API Gateway resources, see [ Control who can create and manage an API Gateway API with IAM policies](api-gateway-control-access-using-iam-policies-to-create-and-manage-api.md#api-gateway-control-access-using-iam-policies)\. 