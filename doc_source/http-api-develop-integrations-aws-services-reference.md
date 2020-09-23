# Integration subtype reference<a name="http-api-develop-integrations-aws-services-reference"></a>

The following [integration subtypes](https://docs.aws.amazon.com/apigatewayv2/latest/api-reference/apis-apiid-integrations-integrationid.html#apis-apiid-integrations-integrationid-prop-integration-integrationsubtype) are supported for HTTP APIs\.

**Topics**
+ [EventBridge\-PutEvents](#EventBridge-PutEvents)
+ [SQS\-SendMessage](#SQS-SendMessage)
+ [SQS\-ReceiveMessage](#SQS-ReceiveMessage)
+ [SQS\-DeleteMessage](#SQS-DeleteMessage)
+ [SQS\-PurgeQueue](#SQS-PurgeQueue)
+ [AppConfig\-GetConfiguration](#AppConfig-GetConfiguration)
+ [Kinesis\-PutRecord](#Kinesis-PutRecord)
+ [StepFunctions\-StartExecution](#StepFunctions-StartExecution)
+ [StepFunctions\-StopExecution](#StepFunctions-StopExecution)

## EventBridge\-PutEvents<a name="EventBridge-PutEvents"></a>

Sends custom events to Amazon EventBridge so that they can be matched to rules\.


**EventBridge\-PutEvents 1\.0**  

| Parameter | Required | 
| --- | --- | 
| Detail | True | 
| DetailType | True | 
| Source | True | 
| Time | False | 
| EventBusName | False | 
| Resources | False | 
| Region | False | 

To learn more, see [PutEvents](https://docs.aws.amazon.com/eventbridge/latest/APIReference/API_PutEvents.html) in the *Amazon EventBridge API Reference*\.

## SQS\-SendMessage<a name="SQS-SendMessage"></a>

Delivers a message to the specified queue\.


**SQS\-SendMessage 1\.0**  

| Parameter | Required | 
| --- | --- | 
| QueueUrl | True | 
| MessageBody | True | 
| DelaySeconds | False | 
| MessageAttributes | False | 
| MessageDeduplicationId | False | 
| MessageGroupId | False | 
| MessageSystemAttributes | False | 
| Region | False | 

To learn more, see [SendMessage](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_SendMessage.html) in the *Amazon Simple Queue Service API Reference*\.

## SQS\-ReceiveMessage<a name="SQS-ReceiveMessage"></a>

Retrieves one or more messages \(up to 10\), from the specified queue\.


**SQS\-ReceiveMessage 1\.0**  

| Parameter | Required | 
| --- | --- | 
| QueueUrl | True | 
| AttributeName | False | 
| MaxNumberOfMessages | False | 
| MessageAttributeNames | False | 
| ReceiveRequestAttemptId | False | 
| VisibilityTimeout | False | 
| WaitTimeSeconds | False | 
| Region | False | 

To learn more, see [ReceiveMessage](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_ReceiveMessage.html) in the *Amazon Simple Queue Service API Reference*\.

## SQS\-DeleteMessage<a name="SQS-DeleteMessage"></a>

Deletes the specified message from the specified queue\.


**SQS\-DeleteMessage 1\.0**  

| Parameter | Required | 
| --- | --- | 
| ReceiptHandle | True | 
| QueueUrl | True | 
| Region | False | 

To learn more, see [DeleteMessage](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_DeleteMessage.html) in the *Amazon Simple Queue Service API Reference*\.

## SQS\-PurgeQueue<a name="SQS-PurgeQueue"></a>

Deletes all messages in the specified queue\.


**SQS\-PurgeQueue 1\.0**  

| Parameter | Required | 
| --- | --- | 
| QueueUrl | True | 
| Region | False | 

To learn more, see [PurgeQueue](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/APIReference/API_PurgeQueue.html) in the *Amazon Simple Queue Service API Reference*\.

## AppConfig\-GetConfiguration<a name="AppConfig-GetConfiguration"></a>

Receive information about a configuration\.


**AppConfig\-GetConfiguration 1\.0**  

| Parameter | Required | 
| --- | --- | 
| Application | True | 
| Environment | True | 
| Configuration | True | 
| ClientId | True | 
| ClientConfigurationVersion | False | 
| Region | False | 

To learn more, see [GetConfiguration](https://docs.aws.amazon.com/appconfig/2019-10-09/APIReference/API_GetConfiguration.html) in the *AWS AppConfig API Reference*\.

## Kinesis\-PutRecord<a name="Kinesis-PutRecord"></a>

Writes a single data record into an Amazon Kinesis data stream\.


**Kinesis\-PutRecord 1\.0**  

| Parameter | Required | 
| --- | --- | 
| StreamName | True | 
| Data | True | 
| PartitionKey | True | 
| SequenceNumberForOrdering | False | 
| ExplicitHashKey | False | 
| Region | False | 

To learn more, see [PutRecord](https://docs.aws.amazon.com/kinesis/latest/APIReference/API_PutRecord.html) in the *Amazon Kinesis Data Streams API Reference*\.

## StepFunctions\-StartExecution<a name="StepFunctions-StartExecution"></a>

Starts a state machine execution\.


**StepFunctions\-StartExecution 1\.0**  

| Parameter | Required | 
| --- | --- | 
| StateMachineArn | True | 
| Name | False | 
| Input | False | 
| Region | False | 

To learn more, see [StartExecution](https://docs.aws.amazon.com/step-functions/latest/apireference/API_StartExecution.html) in the *AWS Step Functions API Reference*\.

## StepFunctions\-StopExecution<a name="StepFunctions-StopExecution"></a>

Stops an execution\.


**StepFunctions\-StopExecution 1\.0**  

| Parameter | Required | 
| --- | --- | 
| ExecutionArn | True | 
| Cause | False | 
| Error | False | 
| Region | False | 

To learn more, see [StopExecution](https://docs.aws.amazon.com/step-functions/latest/apireference/API_StopExecution.html) in the *AWS Step Functions API Reference*\.