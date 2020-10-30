# Disabling the default endpoint for a REST API<a name="rest-api-disable-default-endpoint"></a>

By default, clients can invoke your API by using the `execute-api` endpoint that API Gateway generates for your API\. To ensure that clients can access your API only by using a custom domain name, disable the default `execute-api` endpoint\.

**Note**  
When you disable the default endpoint, it affects all stages of an API\.

The following AWS CLI command disables the default endpoint for a REST API\.

```
aws apigateway update-rest-api \
    --rest-api-id abcdef123 \
    --patch-operations op=replace,path=/disableExecuteApiEndpoint,value='True'
```

After you disable the default endpoint, you must deploy your API for the change to take effect\.

The following AWS CLI command creates a deployment\.

```
aws apigateway create-deployment \
    --rest-api-id abcdef123 \
    --stage-name dev
```
