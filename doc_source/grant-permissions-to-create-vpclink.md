# Grant permissions to create a VPC link<a name="grant-permissions-to-create-vpclink"></a>

For you or a user in your account to create and maintain a VPC link, you or the user must have permissions to create, delete, and view VPC endpoint service configurations, change VPC endpoint service permissions, and examine load balancers\. To grant such permissions, use the following steps\. 

**To grant permissions to create and update a `VpcLink`**

1. Create an IAM policy similar to the following:

   ```
   {  
      "Version":"2012-10-17",
      "Statement":[  
         {  
            "Effect":"Allow",
            "Action":[  
               "ec2:CreateVpcEndpointServiceConfiguration",
               "ec2:DeleteVpcEndpointServiceConfigurations",
               "ec2:DescribeVpcEndpointServiceConfigurations",
               "ec2:ModifyVpcEndpointServicePermissions"
            ],
            "Resource":"*"
         },
         {  
            "Effect":"Allow",
            "Action":[  
               "elasticloadbalancing:DescribeLoadBalancers"
            ],
            "Resource":"*"
         }
      ]
   }
   ```

1. Create or choose an IAM role and attach the preceding policy to the role\.

1. Assign the IAM role to you or a user in your account who is creating VPC links\.