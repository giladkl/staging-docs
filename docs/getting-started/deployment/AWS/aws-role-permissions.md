# AWS Role Permissions
This page provides a comprehensive overview of the AWS role permissions required to integrate your account with Epsio.
 
Epsio's unique deployment model involves creating instances that reside in your cloud account, which Epsio manages.  
** This provides the benefits of a managed service while ensuring that sensitive data never leaves your environment and Epsio does not have access to your data.**

## Permissions
When integrating with AWS, Epsio is granted permissions to create and manage its deployment in your account.  

Epsio's role has the ability to create resources, such as EC2 instances and security groups, in the selected VPC and subnet, and to manage or edit only resources that were created by Epsio and have the Epsio tag (Owner=Epsio).


| Role | Permission |
|-------|-----------|
| Launch | **Limited to selected VPC & Subnet** <br> `ec2:RunInstances` <br>`ec2:CreateTags` <br>`ec2:CreateSecurityGroup`|
| Instance Management | ** Limited to Epsio resources (where `Owner=Epsio`):** <br> `ec2:RebootInstances`<br> `ec2:StopInstances`<br> `ec2:TerminateInstances` <br>`ec2:StartInstances` <br>`ec2:AttachVolume`<br> `ec2:DetachVolume`<br> `ec2:AssociateIamInstanceProfile` <br>`ec2:DisassociateIamInstanceProfile` <br>`ec2:GetConsoleScreenshot` <br>`ec2:ReplaceIamInstanceProfileAssociation` |
| Security Group management | ** Limited to Epsio resources (where `Owner=Epsio`):** <br> `ec2:AuthorizeSecurityGroupIngress`<br> `ec2:RevokeSecurityGroupIngress` <br>`ec2:AuthorizeSecurityGroupEgress` <br>`ec2:RevokeSecurityGroupEgress` <br>`ec2:ModifySecurityGroupRules`<br> `ec2:UpdateSecurityGroupRuleDescriptionsIngress` <br>`ec2:UpdateSecurityGroupRuleDescriptionsEgress`
|   Monitoring | `ec2:DescribeSubnets` <br>`ec2:DescribeVolumes` <br>`ec2:DescribeSecurityGroups`<br> `ec2:DescribeInstances` <br>`ec2:DescribeInstanceStatus` <br>`ec2:DescribeVpcs` |
| Termination | ** Limited to Epsio resources (based on Epsio's resource names)** <br> `cloudformation:DeleteStack`<br>`iam:DeleteRole` <br>`iam:DeleteRolePolicy` <br>`iam:DeleteFunction` <br>`lambda:InvokeFunction` |
