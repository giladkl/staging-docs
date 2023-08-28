# Networking

Deploying Epsio in your AWS account provides greater control over your network configurations, enabling you to comply with specific cloud security and governance standards that your organization may require.

<figure markdown>
  ![Network Diagram](/assets/network_diagram.png){ width="500" loading=lazy}
  <figcaption>Deployment Network Diagram</figcaption>
</figure>


## Network ACL Requirements
!!! tip ""
    **When you deploy Epsio in your cloud account, a security group is automatically created to allow the required traffic for your Epsio deployment.  
    However, it is possible that your VPC/Subnet ACLs may block the required traffic, which can cause your Epsio deployment to function properly.**

In order for your Epsio deployment to function properly, you must configure your network ACLs to allow the following:

- Ingress:
    - Allows incoming traffic from your database’s IP and port (Postgres default port is 5432).
- Egress:
    - Allow outgoing traffic to your database’s IP and port (Postgres default port is 5432).
    - Allow TCP access to 0.0.0.0/0 for these ports:
        - 443: for Epsio infrastructure and management service.

## Troubleshooting
If you are experiencing issues with your Epsio deployment, you can troubleshoot the issue by verifying the following:

1. **Verify Network ACLs**: Network ACLs (Access Control Lists) are another layer of security in your VPC that control inbound and outbound traffic at the subnet level. Make sure your Network ACLs are not blocking the [required traffic](#network-acl-requirements).
2. **Verify Internet Gateway (IGW) Association**: To enable communication over the internet, a VPC must have an Internet Gateway (IGW) attached to it. [Verify that your VPC is associated with an IGW](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html).
3. **Check Security Group Rules**: Your Epsio deployment is automatically configured with a security group that allows the required traffic. However, if you have modified the security group rules, make sure that the [required traffic](#network-acl-requirements) is allowed.
