# View API Gateway log events in the CloudWatch console<a name="view-cloudwatch-log-events-in-cloudwatch-console"></a>

**To view logged API requests and responses using the CloudWatch console**

1. In the navigation pane, choose **Logs**\.

1. Under the **Log Groups** table, choose a log group of the **API\-Gateway\-Execution\-Logs\_\{rest\-api\-id\}/\{stage\-name\}** name\. 

1.  Under the **Log Streams** table, choose a log stream\. You can use the timestamp to help locate the log stream of your interest\. 

1. Choose **Text** to view raw text or choose **Row** to view the event row by row\.

**Important**  
 CloudWatch lets you delete log groups or streams\. Do not manually delete API Gateway API log groups or streams; let API Gateway manage these resources\. Manually deleting log groups or streams may cause API requests and responses not to be logged\. If that happens, you can delete the entire log group for the API and redeploy the API\. This is because API Gateway creates log groups or log streams for an API stage at the time when it is deployed\. 