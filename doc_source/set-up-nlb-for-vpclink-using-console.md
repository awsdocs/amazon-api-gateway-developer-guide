# Set up a Network Load Balancer for API Gateway Private Integrations<a name="set-up-nlb-for-vpclink-using-console"></a>

 The following procedure outlines the steps to set up a network load balancer for API Gateway private integrations using the Amazon EC2 console and provides references for detailed instructions for each step\. 

**To create a network load balancer for private integration using the API Gateway console**

1.  Sign in to the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/) and choose a region; for example, `us-east-1`, on the navigation bar\.

1.  Set up a web server on an Amazon EC2 instance\. For an example setup, see [Installing a LAMP Web Server on Amazon Linux](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/install-LAMP.html)\. 

1.  Create a network load balancer, register the EC2 instance with a target group, and add the target group to a listener of the network load balancer\. For details, follow the instructions in  [Getting Started with Network Load Balancers](http://docs.aws.amazon.com/elasticloadbalancing/latest/network/network-load-balancer-getting-started.html)\. 

   After the network load balancer is created, note its ARN\. You will need it to create a VPC link in API Gateway for integrating the API with the VPC resources behind the network load balancer\.