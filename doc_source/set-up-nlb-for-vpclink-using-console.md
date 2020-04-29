# Set up a Network Load Balancer for API Gateway private integrations<a name="set-up-nlb-for-vpclink-using-console"></a>

 The following procedure outlines the steps to set up a Network Load Balancer \(NLB\) for API Gateway private integrations using the Amazon EC2 console and provides references for detailed instructions for each step\. 

For each VPC you have resources in, you only need to configure one NLB and one VPCLink\. The NLB supports multiple [listeners](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/load-balancer-listeners.html) and [target groups](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/load-balancer-target-groups.html) per NLB\. You can configure each service as a specific listener on the NLB and use a single VPCLink to connect to the NLB\. When creating the private integration in API Gateway you then define each service using the specific port that is assigned for each service\. For more information, see [Tutorial: Build a REST API with API Gateway private integration](getting-started-with-private-integration.md)\.

**Note**  
The Network Load Balancer and API must be owned by the same AWS account\.

**To create a Network Load Balancer for private integration using the API Gateway console**

1. Sign in to the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/) and choose a region; for example, `us-east-1`, on the navigation bar\.

1. Set up a web server on an Amazon EC2 instance\. For an example setup, see [Installing a LAMP Web Server on Amazon Linux](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/install-LAMP.html)\. 

1. Create a Network Load Balancer, register the EC2 instance with a target group, and add the target group to a listener of the Network Load Balancer\. For details, follow the instructions in [Getting Started with Network Load Balancers](https://docs.aws.amazon.com/elasticloadbalancing/latest/network/network-load-balancer-getting-started.html)\. 

   After the Network Load Balancer is created, note its ARN\. You will need it to create a VPC link in API Gateway for integrating the API with the VPC resources behind the Network Load Balancer\.