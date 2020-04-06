# Working with VPC links for HTTP APIs<a name="http-api-vpc-links"></a>

VPC links enable you to create private integrations that connect your HTTP API routes to private resources in a VPC, such as Application Load Balancers or Amazon ECS container\-based applications\. To learn more about creating private integrations, see [Working with private integrations for HTTP APIs](http-api-develop-integrations-private.md)\.

A private integration uses a VPC link to encapsulate connections between API Gateway and targeted VPC resources\. You can reuse VPC links across different routes and APIs\.

When you create a VPC link, API Gateway creates and manages [elastic network interfaces](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_ElasticNetworkInterfaces.html) for the VPC link in your account\. This process can take a few minutes\. When a VPC link is ready to use, its state transitions from `PENDING` to `AVAILABLE`\. 

**Note**  
If no traffic is sent over the VPC link for 60 days, it becomes `INACTIVE`\. When a VPC link is in an `INACTIVE` state, API Gateway deletes all of the VPC link’s network interfaces\. This causes API requests that depend on the VPC link to fail\. If API requests resume, API Gateway reprovisions network interfaces\. It can take a few minutes to create the network interfaces and reactivate the VPC link\. You can use the VPC link status to monitor the state of your VPC link\.

## Create a VPC link by using the AWS CLI<a name="http-api-vpc-links-create"></a>

Use the following command to create a VPC link\. To create a VPC link, all resources involved must be owned by the same AWS account\.

```
aws apigatewayv2 create-vpc-link --name MyVpcLink \
    --subnet-ids subnet-aaaa subnet-bbbb \
    --security-group-ids sg1234 sg5678
```

**Note**  
VPC links are immutable\. After you create a VPC link, you can’t change its subnets or security groups\.

## Delete a VPC link by using the AWS CLI<a name="http-api-vpc-links-delete"></a>

Use the following command to delete a VPC link\.

```
aws apigatewayv2 delete-vpc-link --vpc-link-id abcd123
```