# Resilience in Amazon API Gateway<a name="disaster-recovery-resiliency"></a>

The AWS global infrastructure is built around AWS Regions and Availability Zones\. AWS Regions provide multiple physically separated and isolated Availability Zones, which are connected with low\-latency, high\-throughput, and highly redundant networking\. With Availability Zones, you can design and operate applications and databases that automatically fail over between zones without interruption\. Availability Zones are more highly available, fault tolerant, and scalable than traditional single or multiple data center infrastructures\. 

For more information about AWS Regions and Availability Zones, see [AWS Global Infrastructure](http://aws.amazon.com/about-aws/global-infrastructure/)\.

To prevent your APIs from being overwhelmed by too many requests, API Gateway throttles requests to your APIs\. Specifically, API Gateway sets a limit on a steady\-state rate and a burst of request submissions against all APIs in your account\. You can configure custom throttling for your APIs\. To learn more, see [Throttle API requests for better throughput](api-gateway-request-throttling.md)\.