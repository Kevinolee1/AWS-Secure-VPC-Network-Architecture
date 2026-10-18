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

![AWS Private Subnet Route Table Association](images/10-associate-private-subnet-route-table.png)

**Figure 10 – Private Subnet Route Table Association:** I explicitly associated `CloudSecurity-Private-Subnet` with the dedicated `CloudSecurity-Private-RT` route table.

I verified that the route table contained only the active `10.0.0.0/16 → local` route, supporting internal VPC communication without providing a default route to the internet gateway.

I confirmed that the private subnet was the only explicitly associated subnet.

This configuration establishes separate routing policies for public and private network segments, helping protect internal resources from direct internet exposure.
