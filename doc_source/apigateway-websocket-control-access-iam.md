# Using IAM authorization<a name="apigateway-websocket-control-access-iam"></a>

IAM authorization in WebSocket APIs is similar to that for [REST APIs](api-gateway-control-access-using-iam-policies-to-invoke-api.md), with the following exceptions:
+ The `execute-api` action supports `ManageConnections` in addition to existing actions \(`Invoke`, `InvalidateCache`\)\. `ManageConnections` controls access to the @connections API\.
+ WebSocket routes use a different ARN format:

  ```
  arn:aws:execute-api:region:account-id:api-id/stage-name/route-key
  ```
+ The `@connections` API uses the same ARN format as REST APIs:

  ```
  arn:aws:execute-api:region:account-id:api-id/stage-name/POST/@connections
  ```

For example, you could set up the following policy to the client\. This example allows everyone to send a message \(`Invoke`\) for all routes except for a secret route in the `prod` stage and prevents everyone from sending a message back to connected clients \(`ManageConnections`\) for all stages\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "execute-api:Invoke"           
      ],
      "Resource": [
        "arn:aws:execute-api:us-east-1:account-id:api-id/prod/*"
      ]
    },
    {
      "Effect": "Deny",
      "Action": [
        "execute-api:Invoke"           
      ],
      "Resource": [
        "arn:aws:execute-api:us-east-1:account-id:api-id/prod/secret"
      ]
    },
    {
      "Effect": "Deny",
      "Action": [
        "execute-api:ManageConnections"           
      ],
      "Resource": [
        "arn:aws:execute-api:us-east-1:account-id:api-id/*"
      ]
    }
  ]
}
```