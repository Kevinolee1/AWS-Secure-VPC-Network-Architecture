# AWS-Secure-VPC-Network-Architecture
Hands on AWS cloud networking project focused on designing and building a secure Virtual Private Cloud (VPC) from scratch, including public and private subnets, route tables, internet gateways, security groups, network ACLs, and network isolation using AWS security best practices.

## Step 1 – Configure Secure AWS Administrator Access

![Secure AWS Administrator Configuration](https://github.com/Kevinolee1/AWS-Secure-VPC-Network-Architecture/blob/0a2e6f590d69304b203b3b3d45e91041b0fce5d2/Screenshot%202026-10-17%20222618.png)

**Figure 1 – AWS IAM Administrator Security Configuration:** I created and configured the `cloud-lab-admin` IAM user to manage AWS infrastructure without routinely using the root account.

I attached the AWS managed `AdministratorAccess` policy and configured passkey-based multi-factor authentication (MFA) using Windows Hello.

I verified that console access displayed **Enabled with MFA** and confirmed that the administrator policy was successfully attached.

This establishes a dedicated, MFA-protected administrative identity for building and securing the AWS network architecture.

## Step 2 – Create a Custom AWS Virtual Private Cloud

![Custom AWS VPC Configuration](https://github.com/Kevinolee1/AWS-Secure-VPC-Network-Architecture/blob/f1e2aeda8a168a50af5af6df8e1690d218ef576d/Screenshot%202026-10-17%20225130.png)

**Figure 2 – Custom AWS VPC Deployment:** I successfully created a custom Virtual Private Cloud named `CloudSecurity-VPC` using the AWS VPC console.

I configured the VPC with the IPv4 CIDR block `10.0.0.0/16`, providing an address space for organizing multiple public and private subnets.

I verified that the VPC entered the **Available** state, DNS resolution was enabled, and the default tenancy was configured.

This VPC establishes the network foundation for implementing subnet segmentation, routing controls, and secure communication between AWS resources.

## Step 3 – Create a Public Subnet

![AWS Public Subnet Configuration](https://github.com/Kevinolee1/AWS-Secure-VPC-Network-Architecture/blob/00eafc911d8533c936d21b89dcbbdd5030cc6d54/Screenshot%202026-10-17%20225930.png)

**Figure 3 – Public Subnet Deployment:** I created a subnet named `CloudSecurity-Public-Subnet` inside the `CloudSecurity-VPC` in the AWS US East (N. Virginia) region.

I assigned the subnet the IPv4 CIDR block `10.0.1.0/24` and deployed it in Availability Zone `us-east-1a`.

I verified that the subnet entered the **Available** state with 251 usable IPv4 addresses available for AWS resources.

This subnet will serve as the public network segment once internet gateway connectivity and public routing are configured in subsequent steps.

## Step 4 – Create a Private Subnet

![AWS Private Subnet Configuration](https://github.com/Kevinolee1/AWS-Secure-VPC-Network-Architecture/blob/cb28de97cbf21041f3e1d12100563f38971ae912/Screenshot%202026-10-17%20230357.png)

**Figure 4 – Private Subnet Deployment:** I successfully created a private subnet named `CloudSecurity-Private-Subnet` within my custom `CloudSecurity-VPC`.

I assigned the IPv4 CIDR block `10.0.2.0/24`, separating the private network segment from the public subnet configured with `10.0.1.0/24`.

I verified that the subnet entered the **Available** state and was associated with the correct VPC.

This subnet is intended to host internal resources, such as databases, that require restricted network access. Internet isolation will be enforced through route table configuration and additional network security controls in subsequent steps.

## Step 5 – Create and Attach an Internet Gateway

![AWS Internet Gateway Configuration](https://github.com/Kevinolee1/AWS-Secure-VPC-Network-Architecture/blob/4485d23fdb07edc45301c5a7c88dfe15004ddc15/Screenshot%202026-10-17%20231028.png)

**Figure 5 – Internet Gateway Deployment:** I created an internet gateway named `CloudSecurity-IGW` and attached it to my custom `CloudSecurity-VPC` in the AWS US East (N. Virginia) region.

I verified that the internet gateway entered the **Attached** state and was associated with the correct VPC.

The internet gateway provides a path for internet connectivity when resources are configured with appropriate route tables, IP addressing, and security controls.

This prepares the VPC for public subnet connectivity while allowing private subnets to remain isolated from direct internet access.

## Step 6 – Create a Public Route Table

![AWS Public Route Table](https://github.com/Kevinolee1/AWS-Secure-VPC-Network-Architecture/blob/bfbdd0617f774965677fd7fa8053214200b6584c/Screenshot%202026-10-17%20232231.png)

**Figure 6 – Public Route Table Creation:** I created a custom route table named `CloudSecurity-Public-RT` within my `CloudSecurity-VPC`.

I verified that AWS automatically added the `10.0.0.0/16 → local` route, allowing internal routing between resources within the VPC's address range.

I also confirmed that this was not the VPC's main route table and that no subnets were explicitly associated with it yet.

This establishes the foundation for configuring controlled internet routing for the public subnet while maintaining separate routing for private resources.


## Step 7 – Configure Public Internet Routing

![AWS Public Internet Route Configuration](https://github.com/Kevinolee1/AWS-Secure-VPC-Network-Architecture/blob/b2387dce04a78f827b59b9ab6e08fabe8b0cf82a/Screenshot%202026-10-17%20233423.png)

**Figure 7 – Public Route Table Internet Gateway Configuration:** I configured the `CloudSecurity-Public-RT` route table to direct outbound IPv4 traffic toward the `CloudSecurity-IGW` internet gateway.

I added the default route `0.0.0.0/0` with the internet gateway as its target while retaining the existing `10.0.0.0/16 → local` route for internal VPC communication.

I verified that both routes displayed an **Active** status.

This establishes the routing configuration required for the public subnet to support internet connectivity once the subnet is explicitly associated with this route table.


## Step 8 – Associate the Public Subnet with Its Route Table

![Public Subnet Route Table Association](https://github.com/Kevinolee1/AWS-Secure-VPC-Network-Architecture/blob/e5f3a4493daa6518de5bd610914e02fcb76a26bf/Screenshot%202026-10-17%20234338.png)

**Figure 8 – Public Subnet Route Table Association:** I explicitly associated `CloudSecurity-Public-Subnet` with the custom `CloudSecurity-Public-RT` route table.

I verified that the route table contained two active routes: `10.0.0.0/16 → local` for internal VPC communication and `0.0.0.0/0 → Internet Gateway` for internet-bound IPv4 traffic.

I confirmed that only the public subnet was explicitly associated with this route table, maintaining separation from the private subnet.

This configuration establishes the public subnet's internet routing while preserving the intended network segmentation.


## Step 9 – Create a Private Route Table

![AWS Private Route Table](https://github.com/Kevinolee1/AWS-Secure-VPC-Network-Architecture/blob/de62eec258c4c813854a6d668c50ccfac09ffa14/Screenshot%202026-10-17%20235239.png)

**Figure 9 – Private Route Table Creation:** I created a dedicated route table named `CloudSecurity-Private-RT` within my custom `CloudSecurity-VPC`.

I verified that the route table contained only the `10.0.0.0/16 → local` route, allowing internal communication between resources within the VPC's address range.

I confirmed that no default route to an internet gateway was configured, preserving the intended network isolation for private resources.

This route table will provide dedicated routing for the private subnet once the subnet association is completed.


## Step 10 – Associate the Private Subnet with Its Route Table

![AWS Private Subnet Route Table Association](https://github.com/Kevinolee1/AWS-Secure-VPC-Network-Architecture/blob/b55c557c7784d76f035f0b392162443796088a4a/Screenshot%202026-10-17%20235507.png)

**Figure 10 – Private Subnet Route Table Association:** I explicitly associated `CloudSecurity-Private-Subnet` with the dedicated `CloudSecurity-Private-RT` route table.

I verified that the route table contained only the active `10.0.0.0/16 → local` route, supporting internal VPC communication without providing a default route to the internet gateway.

I confirmed that the private subnet was the only explicitly associated subnet.

This configuration establishes separate routing policies for public and private network segments, helping protect internal resources from direct internet exposure.

## Step 11 – Create a Web Server Security Group

![AWS Web Server Security Group](https://github.com/Kevinolee1/AWS-Secure-VPC-Network-Architecture/blob/77e5d888c7edb0c941df9fdba0a6dfcbf86f148a/Screenshot%202026-10-18%20001030.png)

**Figure 11 – Web Server Security Group Configuration:** I created a security group named `CloudSecurity-Web-SG` within my custom `CloudSecurity-VPC`.

I configured an inbound rule allowing HTTPS traffic over TCP port 443 from `0.0.0.0/0`, preparing the security group to support secure web application access.

I verified that only one inbound rule was configured and that SSH port 22 was not exposed to the public internet.

This configuration follows the principle of minimizing unnecessary network exposure while allowing the traffic required for a public-facing web application.

## Step 12 – Create a Database Security Group

![AWS Database Security Group](https://github.com/Kevinolee1/AWS-Secure-VPC-Network-Architecture/blob/b60e5f380edd4e61cd6e28dbc947d228fabd464f/Screenshot%202026-10-18%20001643.png)

**Figure 12 – Database Security Group Configuration:** I created a security group named `CloudSecurity-DB-SG` within my custom `CloudSecurity-VPC`.

I configured an inbound PostgreSQL rule allowing TCP traffic on port 5432 exclusively from resources associated with `CloudSecurity-Web-SG`.

I verified that the inbound rule referenced the web server security group rather than allowing unrestricted access from `0.0.0.0/0`.

This configuration applies least-privilege network access controls to the database tier, restricting PostgreSQL connectivity to authorized application resources.


## Step 13 – Restrict Database Outbound Traffic

![AWS Database Outbound Security Rules](https://github.com/Kevinolee1/AWS-Secure-VPC-Network-Architecture/blob/2ca3865547139cf2d479510c2cb53f47c32d594c/Screenshot%202026-10-18%20001850.png)

**Figure 13 – Database Security Group Outbound Restriction:** I modified the outbound rules of `CloudSecurity-DB-SG` to remove the default unrestricted outbound traffic permission.

I verified that the security group contained zero outbound rules while retaining its existing inbound PostgreSQL rule.

Because AWS security groups are stateful, response traffic associated with permitted inbound connections remains allowed.

This configuration demonstrates restrictive outbound access controls and reduces unnecessary network permissions for the database tier.

## Step 14 – Review Default Network ACL Rules

![AWS Default Network ACL Review](https://github.com/Kevinolee1/AWS-Secure-VPC-Network-Architecture/blob/a685a9a7834af8c9995796c4839d601edfb5118d/Screenshot%202026-10-18%20002245.png)

**Figure 14 – Default Network ACL Security Review:** I examined the default Network Access Control List (NACL) associated with `CloudSecurity-VPC`.

I verified that the Network ACL was associated with two subnets and reviewed its outbound traffic rules.

The configuration included rule 100, allowing all outbound traffic, followed by the default deny rule for unmatched traffic.

This review establishes the existing subnet-level network filtering configuration before implementing more restrictive Network ACL policies.

## Step 15 – Create a Dedicated Private Network ACL

![AWS Private Network ACL](https://github.com/Kevinolee1/AWS-Secure-VPC-Network-Architecture/blob/392e7070d762ee2e07c8c3b37fe2b7400b02276a/Screenshot%202026-10-18%20002609.png)

**Figure 15 – Private Network ACL Creation:** I created a custom Network Access Control List named `CloudSecurity-Private-NACL` within `CloudSecurity-VPC`.

I verified that the newly created NACL contained the default inbound deny rule, blocking traffic unless explicitly permitted by additional rules.

I also confirmed that the NACL was not yet associated with any subnet, allowing me to configure its security rules before applying them to the private network segment.

This establishes an additional layer of subnet-level network security for protecting internal AWS resources.

## Step 16 – Configure Private Network ACL Inbound Rules

![Private NACL Inbound Rules](https://github.com/Kevinolee1/AWS-Secure-VPC-Network-Architecture/blob/83e1d5bf7e54deaa28317b218662582c1458f795/Screenshot%202026-10-18%20005803.png)

**Figure 16 – Private Network ACL Inbound Configuration:** I configured the inbound rules for `CloudSecurity-Private-NACL` to permit PostgreSQL traffic over TCP port 5432 from the public subnet's CIDR range, `10.0.1.0/24`.

I added rule 100 to allow the required database traffic while retaining the default deny rule for unmatched inbound traffic.

This establishes subnet-level traffic filtering that complements the database security group's more restrictive source-based permissions.

## Step 17 – Configure Private Network ACL Outbound Rules

![Private NACL Outbound Rules](https://github.com/Kevinolee1/AWS-Secure-VPC-Network-Architecture/blob/f5d4f7472d6844d396a85a9301305bf30d87a976/Screenshot%202026-10-18%20011124.png)

**Figure 17 – Private Network ACL Outbound Configuration:** I configured outbound rules for `CloudSecurity-Private-NACL` to allow TCP traffic on ephemeral ports `1024-65535` to the public subnet CIDR range `10.0.1.0/24`.

I verified that rule 100 permitted the required return traffic while the default deny rule blocked unmatched outbound traffic.

Because Network ACLs are stateless, explicitly allowing outbound response traffic is necessary for PostgreSQL connections initiated by authorized application resources.

This configuration adds subnet-level traffic restrictions that complement the database security group's least-privilege permissions.


## Step 18 – Associate the Private Network ACL with the Private Subnet

![Private Network ACL Subnet Association]()

**Figure 18 – Private Network ACL Subnet Association:** I associated the custom `CloudSecurity-Private-NACL` with `CloudSecurity-Private-Subnet` inside `CloudSecurity-VPC`.

I verified that the private subnet was associated with the custom Network ACL while the public subnet remained associated with the VPC's default Network ACL.

This establishes separate subnet-level traffic filtering for the public and private network segments.

The private Network ACL complements the database security group by providing an additional layer of network access control.

## Step 19 – Verify AWS VPC Network Architecture

![AWS Secure VPC Architecture](images/19-verify-vpc-network-architecture.png)

**Figure 19 – AWS VPC Network Architecture Verification:** I reviewed the AWS VPC resource map to validate the network architecture created throughout the lab.

I verified that `CloudSecurity-VPC` used the `10.0.0.0/16` IPv4 CIDR block and contained two subnets: `CloudSecurity-Public-Subnet` and `CloudSecurity-Private-Subnet`.

The public subnet was associated with `CloudSecurity-Public-RT`, which provided a route to `CloudSecurity-IGW`.

The private subnet was associated with `CloudSecurity-Private-RT`, which maintained internal VPC routing without a default internet gateway route.

The resource map confirmed that both subnets used their intended route tables and that the internet gateway was connected to the public routing path.

This verification demonstrates the implementation of a segmented AWS VPC network architecture with separate public and private routing configurations.

