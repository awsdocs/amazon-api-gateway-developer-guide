# View API Gateway metrics in the CloudWatch console<a name="metrics_dimensions_view_in_cloud_watch"></a>

Metrics are grouped first by the service namespace, and then by the various dimension combinations within each namespace\.

**To view API Gateway metrics using the CloudWatch console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. If necessary, change the region\. From the navigation bar, select the region where your AWS resources reside\. For more information, see [Regions and Endpoints](http://docs.aws.amazon.com/general/latest/gr/rande.html)\.

1. In the navigation pane, choose **Metrics**\.

1. In the **All metrics** tab, choose **API Gateway**\. 

1.  To view metrics by stage, choose the **By Stage** panel\. And then select desired APIs and metric names\. 

1. To view metrics by specific API, choose the **By Api Name** panel\. And then select desired APIs and metric names\.

**To view metrics using the AWS CLI**

1. At a command prompt, use the following command to list metrics:

   ```
   1. aws cloudwatch list-metrics --namespace "AWS/ApiGateway"
   ```

1. To view a specific statistics \(for example, `Average`\) over a period of time of a 5 minutes intervals, call the following command:

   ```
   aws cloudwatch get-metric-statistics --namespace AWS/ApiGateway --metric-name Count --start-time 2011-10-03T23:00:00Z --end-time 2017-10-05T23:00:00Z --period 300 --statistics Average
   ```