# AWS-Secure-VPC-Network-Architecture
Hands on AWS cloud networking project focused on designing and building a secure Virtual Private Cloud (VPC) from scratch, including public and private subnets, route tables, internet gateways, security groups, network ACLs, and network isolation using AWS security best practices.



## Step 1 – Configure Secure AWS Administrator Access

![Secure AWS Administrator Configuration](https://github.com/Kevinolee1/AWS-Secure-VPC-Network-Architecture/blob/0a2e6f590d69304b203b3b3d45e91041b0fce5d2/Screenshot%202026-10-17%20222618.png)

**Figure 1 – AWS IAM Administrator Security Configuration:** I created and configured the `cloud-lab-admin` IAM user to manage AWS infrastructure without routinely using the root account.

I attached the AWS managed `AdministratorAccess` policy and configured passkey-based multi-factor authentication (MFA) using Windows Hello.

I verified that console access displayed **Enabled with MFA** and confirmed that the administrator policy was successfully attached.

This establishes a dedicated, MFA-protected administrative identity for building and securing the AWS network architecture.


## Step 2 – Create a Custom AWS Virtual Private Cloud

![Custom AWS VPC Configuration](images/02-create-custom-vpc.png)

**Figure 2 – Custom AWS VPC Deployment:** I successfully created a custom Virtual Private Cloud named `CloudSecurity-VPC` using the AWS VPC console.

I configured the VPC with the IPv4 CIDR block `10.0.0.0/16`, providing an address space for organizing multiple public and private subnets.

I verified that the VPC entered the **Available** state, DNS resolution was enabled, and the default tenancy was configured.

This VPC establishes the network foundation for implementing subnet segmentation, routing controls, and secure communication between AWS resources.


## Step 3 – Create a Public Subnet

![AWS Public Subnet Configuration](images/03-create-public-subnet.png)

**Figure 3 – Public Subnet Deployment:** I created a subnet named `CloudSecurity-Public-Subnet` inside the `CloudSecurity-VPC` in the AWS US East (N. Virginia) region.

I assigned the subnet the IPv4 CIDR block `10.0.1.0/24` and deployed it in Availability Zone `us-east-1a`.

I verified that the subnet entered the **Available** state with 251 usable IPv4 addresses available for AWS resources.

This subnet will serve as the public network segment once internet gateway connectivity and public routing are configured in subsequent steps.


## Step 4 – Create a Private Subnet

![AWS Private Subnet Configuration](images/04-create-private-subnet.png)

**Figure 4 – Private Subnet Deployment:** I successfully created a private subnet named `CloudSecurity-Private-Subnet` within my custom `CloudSecurity-VPC`.

I assigned the IPv4 CIDR block `10.0.2.0/24`, separating the private network segment from the public subnet configured with `10.0.1.0/24`.

I verified that the subnet entered the **Available** state and was associated with the correct VPC.

This subnet is intended to host internal resources, such as databases, that require restricted network access. Internet isolation will be enforced through route table configuration and additional network security controls in subsequent steps.


## Step 5 – Create and Attach an Internet Gateway

![AWS Internet Gateway Configuration](images/05-create-internet-gateway.png)

**Figure 5 – Internet Gateway Deployment:** I created an internet gateway named `CloudSecurity-IGW` and attached it to my custom `CloudSecurity-VPC` in the AWS US East (N. Virginia) region.

I verified that the internet gateway entered the **Attached** state and was associated with the correct VPC.

The internet gateway provides a path for internet connectivity when resources are configured with appropriate route tables, IP addressing, and security controls.

This prepares the VPC for public subnet connectivity while allowing private subnets to remain isolated from direct internet access.
