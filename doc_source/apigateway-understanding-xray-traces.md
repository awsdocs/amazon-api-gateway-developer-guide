# Understanding AWS X\-Ray traces for Amazon API Gateway APIs<a name="apigateway-understanding-xray-traces"></a>

This section discusses AWS X\-Ray trace segments, subsegments, and other trace fields for Amazon API Gateway APIs\.

Before you read this section, review the following topics in the X\-Ray Developer Guide:
+ [AWS X\-Ray Console](https://docs.aws.amazon.com/xray/latest/devguide/xray-console.html)
+ [AWS X\-Ray Segment Documents](https://docs.aws.amazon.com/xray/latest/devguide/xray-api-segmentdocuments.html)
+ [X\-Ray Concepts](https://docs.aws.amazon.com/xray/latest/devguide/xray-concepts.html)

**Topics**
+ [Examples of trace objects for an API Gateway API](#apigateway-understanding-xray-traces-example-segments)
+ [Understanding the trace](#apigateway-understanding-xray-traces-segments)

## Examples of trace objects for an API Gateway API<a name="apigateway-understanding-xray-traces-example-segments"></a>

This section discusses some of the objects you may see in a trace for an API Gateway API\.

**Annotations**

Annotations can appear in segments and subsegments\. They are used as filtering expressions in sampling rules to filter traces\. For more information, see [Configuring Sampling Rules in the AWS X\-Ray Console](https://docs.aws.amazon.com/xray/latest/devguide/xray-console-sampling.html)\.

Following is an example of an [https://docs.aws.amazon.com/xray/latest/devguide/xray-api-segmentdocuments.html#api-segmentdocuments-annotations](https://docs.aws.amazon.com/xray/latest/devguide/xray-api-segmentdocuments.html#api-segmentdocuments-annotations) object, in which an API stage is identified by the API ID and the API stage name:

```
"annotations": {
    "aws:api_id": "a1b2c3d4e5",
    "aws:api_stage": "dev"
}
```

**AWS resource data**

The [https://docs.aws.amazon.com/xray/latest/devguide/xray-api-segmentdocuments.html#api-segmentdocuments-aws](https://docs.aws.amazon.com/xray/latest/devguide/xray-api-segmentdocuments.html#api-segmentdocuments-aws) object appears only in segments\. Following is an example of an `aws` object that matches the Default sampling rule\. For an in\-depth explanation of sampling rules, see [Configuring Sampling Rules in the AWS X\-Ray Console](https://docs.aws.amazon.com/xray/latest/devguide/xray-console-sampling.html)\.

```
"aws": {
    "xray": {
        "sampling_rule_name": "Default"
    },
    "api_gateway": {
        "account_id": "123412341234",
        "rest_api_id": "a1b2c3d4e5",
        "stage": "dev",
        "request_id": "a1b2c3d4-a1b2-a1b2-a1b2-a1b2c3d4e5f6"
    }
}
```

## Understanding the trace<a name="apigateway-understanding-xray-traces-segments"></a>

Following is a trace segment for an API Gateway stage\. For a detailed explanation of the fields that make up the trace segment, see [AWS X\-Ray Segment Documents](https://docs.aws.amazon.com/xray/latest/devguide/xray-api-segmentdocuments.html) in the AWS X\-Ray Developer Guide\.

```
        {
            "Document": {
                "id": "a1b2c3d4a1b2c3d4",
                "name": "testxray/dev",
                "start_time": 1533928226.229,
                "end_time": 1533928226.614,
                "metadata": {
                    "default": {
                        "extended_request_id": "abcde12345abcde=",
                        "request_id": "a1b2c3d4-a1b2-a1b2-a1b2-a1b2c3d4e5f6"
                    }
                },
                "http": {
                    "request": {
                        "url": "https://example.com/dev?username=demo&message=hellofromdemo/",
                        "method": "GET",
                        "client_ip": "192.0.2.0",
                        "x_forwarded_for": true
                    },
                    "response": {
                        "status": 200,
                        "content_length": 0
                    }
                },
                "aws": {
                    "xray": {
                        "sampling_rule_name": "Default"
                    },
                    "api_gateway": {
                        "account_id": "123412341234",
                        "rest_api_id": "a1b2c3d4e5",
                        "stage": "dev",
                        "request_id": "a1b2c3d4-a1b2-a1b2-a1b2-a1b2c3d4e5f6"
                    }
                },
                "annotations": {
                    "aws:api_id": "a1b2c3d4e5",
                    "aws:api_stage": "dev"
                },
                "trace_id": "1-a1b2c3d4-a1b2c3d4a1b2c3d4a1b2c3d4",
                "origin": "AWS::ApiGateway::Stage",
                "resource_arn": "arn:aws:apigateway:us-east-1::/restapis/a1b2c3d4e5/stages/dev",
                "subsegments": [
                    {
                        "id": "abcdefgh12345678",
                        "name": "Lambda",
                        "start_time": 1533928226.233,
                        "end_time": 1533928226.6130002,
                        "http": {
                            "request": {
                                "url": "https://example.com/2015-03-31/functions/arn:aws:lambda:us-east-1:123412341234:function:xray123/invocations",
                                "method": "GET"
                            },
                            "response": {
                                "status": 200,
                                "content_length": 62
                            }
                        },
                        "aws": {
                            "function_name": "xray123",
                            "region": "us-east-1",
                            "operation": "Invoke",
                            "resource_names": [
                                "xray123"
                            ]
                        },
                        "namespace": "aws"
                    }
                ]
            },
            "Id": "a1b2c3d4a1b2c3d4"
        }
```