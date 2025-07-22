## Overview

This project demonstrates the foundational steps required to set up a secure and scalable cloud infrastructure on AWS. It begins with the creation of a Virtual Private Cloud (VPC) to establish an isolated network environment. Within the VPC, custom subnets are configured to organize and manage resources efficiently. A security group is then defined to control inbound and outbound traffic rules, enhancing the overall security posture. Finally, an Amazon EC2 instance is launched into the configured VPC, enabling the deployment of compute resources within the secured network setup. This setup serves as a base for more complex cloud architectures and deployments.

## Starting the Lab Session

Click Start Lab at the top of the instructions page to begin the lab session.

A timer will appear at the top, showing the time remaining in the session.

Wait until the circle icon next to the AWS link in the upper-left corner turns green. This indicates that the lab environment is ready.Once the circle turns green, click the AWS link in the upper-left corner.

## Create a VPC

In the search box next to Services, search for and select VPC to open the VPC Console.


<img width="1295" height="457" alt="image" src="https://github.com/user-attachments/assets/374f6f09-147c-4979-82ae-b804c7287ea5" />


At the top right of the screen, ensure the region is set to `N. Virginia (us-east-1)`.

On the left panel, click VPC Dashboard.

Click Create VPC.

<img width="1472" height="471" alt="image" src="https://github.com/user-attachments/assets/18e90de6-c4c9-4f2d-be41-cff3b150ae68" />

## Configure VPC Settings

In the VPC settings panel (left side of the screen):

Choose VPC and more.Under Name tag auto-generation, leave Auto-generate selected, but change the value from project to lab.

Set the IPv4 CIDR block to `10.0.0.0/16`

<img width="644" height="576" alt="image" src="https://github.com/user-attachments/assets/b07b6649-daa0-450b-8822-7d97025bac57" />


Set Number of Availability Zones to:`1`

Keep Number of public subnets set to:`1`

Keep Number of private subnets set to:`1`


<img width="663" height="449" alt="image" src="https://github.com/user-attachments/assets/0b4ac17f-40b8-46fe-8e68-e699d7f459bb" />


Expand the Customize subnets CIDR blocks section:

Set Public subnet CIDR block in us-east-1a to: `10.0.0.0/24`

Set Private subnet CIDR block in us-east-1a to: `10.0.1.0/24`

<img width="595" height="677" alt="image" src="https://github.com/user-attachments/assets/7150613d-a25e-4c9d-b782-cc97f674409e" />

Set NAT gateways to:`1` in AZ

Set VPC endpoints to:None

Ensure DNS hostnames and DNS resolution are both enabled

## Review the Preview Panel (Right Side)

Ensure the following configuration:

VPC: lab-vpc

Subnets:us-east-1a

Public subnet name: `lab-subnet-public1-us-east-1a`

Private subnet name: `lab-subnet-private1-us-east-1a`

Route tables:`lab-rtb-public`,`lab-rtb-private1-us-east-1a`

Network connections:`lab-igw`,`lab-nat-public1-us-east-1a`

<img width="1180" height="439" alt="image" src="https://github.com/user-attachments/assets/43c14bb3-8a0e-4953-9683-5857247bcfdd" />


Scroll to the bottom and click Create VPC.

<img width="856" height="257" alt="image" src="https://github.com/user-attachments/assets/5b678b68-bb52-4c36-9c78-724e07dba6d2" />

Click View VPC

<img width="1869" height="731" alt="image" src="https://github.com/user-attachments/assets/aedabbef-01eb-4db6-b6d1-50678e684443" />

Now we are able to see the VPC details

<img width="1584" height="747" alt="image" src="https://github.com/user-attachments/assets/50330b42-9425-4b82-8a65-6ab10c2461ae" />

An Internet gateway is a VPC resource that allows communication between EC2 instances in your VPC and the Internet. 

The `lab-subnet-public1-us-east-1a` public subnet has a CIDR of 10.0.0.0/24, which means that it contains all IP addresses starting with 10.0.0.x. The fact the route table associated with this public subnet routes 0.0.0.0/0 network traffic to the internet gateway is what makes it a public subnet.

A NAT Gateway, is a VPC resource used to provide internet connectivity to any EC2 instances running in private subnets in the VPC without those EC2 instances needing to have a direct connection to the internet gateway.

The  `lab-subnet-private1-us-east-1a` private subnet has a CIDR of 10.0.1.0/24, which means that it contains all IP addresses starting with 10.0.1.x.

## Create Additional Subnets

After creating a VPC, you can further customize it by adding more subnets. Each subnet must reside entirely within a single Availability Zone (AZ).

### Create a Second Public Subnet

In the left navigation pane of the VPC Console, choose Subnets.

<img width="1427" height="823" alt="image" src="https://github.com/user-attachments/assets/fbe43cf3-c3c0-4e07-b3bc-b395da85d245" />

Click `Create subnet` and configure the following:

VPC ID: lab-vpc (select from the dropdown)

<img width="1394" height="392" alt="image" src="https://github.com/user-attachments/assets/54f60f01-a8a3-4a1b-a1ad-25e6471e93f7" />

Subnet name: lab-subnet-public2

Availability Zone: Choose the second AZ `us-east-1b`

IPv4 CIDR block: 10.0.2.0/24

<img width="1330" height="616" alt="image" src="https://github.com/user-attachments/assets/05555661-1bae-4460-91c1-c8acc0cd755a" />


This subnet will include IP addresses starting with 10.0.2.x.

Click Create subnet.The second public subnet is now created.

### Create a Second Private Subnet

Click Create subnet again and configure:

VPC ID: lab-vpc

<img width="1328" height="318" alt="image" src="https://github.com/user-attachments/assets/0fb57442-5b50-45b9-a3fe-a9ccd69ecaa4" />

Subnet name: `lab-subnet-private2`

Availability Zone: Choose the second AZ `us-east-1b`

IPv4 CIDR block: 10.0.3.0/24

<img width="1344" height="519" alt="image" src="https://github.com/user-attachments/assets/0388afa3-b82b-436f-bdd5-03b9a4ad069a" />

This subnet will include IP addresses starting with 10.0.3.x.

Click Create subnet.The second private subnet is now created.

## Associate Route Tables

Now configure routing so the second private subnet can access the internet via the NAT Gateway, and the second public subnet can access the internet via the Internet Gateway.

### Configure Private Route Table

In the left navigation pane, choose Route Tables.

Select the route table named `lab-rtb-private1-us-east-1a`.

If routes are not visible, click the Refresh button at the top.

In the Routes tab (lower pane), verify that:

Destination: 0.0.0.0/0

Target: nat-xxxxxxxx

<img width="1171" height="733" alt="image" src="https://github.com/user-attachments/assets/4adf822d-ece7-4797-a5af-e09d8504ee38" />


This route sends internet-bound traffic from private subnets to the NAT Gateway.

Go to the Subnet associations tab.

Click Edit subnet associations.

<img width="1552" height="342" alt="image" src="https://github.com/user-attachments/assets/275f2e5a-d966-40d4-87f7-027aea36ea9c" />


Make sure `lab-subnet-private1-us-east-1a` is selected, and also select `lab-subnet-private2`.

<img width="1893" height="637" alt="image" src="https://github.com/user-attachments/assets/75bd14bb-855d-40a4-b60a-65170bfb2263" />

Click Save associations.

### Configure Public Route Table

Select the route table named lab-rtb-public.

In the Routes tab, verify:

Destination: 0.0.0.0/0

Target: igw-xxxxxxxx

<img width="1442" height="652" alt="image" src="https://github.com/user-attachments/assets/cc6b634d-0e33-4862-bd42-167c4928a9cf" />

This route sends internet-bound traffic from public subnets directly to the Internet Gateway.

Go to the Subnet associations tab.

Click Edit subnet associations.

Ensure `lab-subnet-public1-us-east-1a` is selected, and also select `lab-subnet-public2`.

<img width="1905" height="614" alt="image" src="https://github.com/user-attachments/assets/62fc4651-0ef9-44fa-9f68-ede2ad086d7e" />

Click Save associations.

Your VPC is now configured with public and private subnets in two Availability Zones. Route tables have been updated to properly handle internet-bound traffic from both public and private subnets.

## Create a VPC Security Group

In the VPC Console, navigate to the left navigation pane and click Security groups.

<img width="1871" height="562" alt="image" src="https://github.com/user-attachments/assets/cc584b6b-7836-45aa-87ff-7d1a0631c424" />

Click Create security group, then configure the following:

Security group name: Web Security Group

Description: Enable HTTP access

VPC: select lab-vpc

<img width="790" height="321" alt="image" src="https://github.com/user-attachments/assets/b2d9c783-2bfc-4fab-8fe1-bd6c3573a79d" />

Add Inbound Rule

In the Inbound rules section, click Add rule and configure:

Type: HTTP

Source: Anywhere-IPv4

Description: Permit web requests

<img width="1851" height="261" alt="image" src="https://github.com/user-attachments/assets/7a073764-c2a8-4aa8-9797-15ba62669d32" />

Scroll down and click Create security group.

<img width="1518" height="611" alt="image" src="https://github.com/user-attachments/assets/b3a0e3eb-f65e-4271-ac03-05edb5634d13" />

##  Launch a Web Server Instance

In the AWS Console, use the search bar next to Services and search for EC2.

Select EC2 to open the EC2 Console

<img width="1156" height="401" alt="image" src="https://github.com/user-attachments/assets/2e9c9eee-f92c-4bc9-a1b5-a0e842be35fd" />

### Launch the Instance

From the Launch instance menu, click Launch instance.

<img width="1017" height="730" alt="image" src="https://github.com/user-attachments/assets/36100952-ef4e-4511-97d4-25210e710e3d" />

Name the Instance
Set the instance name as:`Web Server 1`

<img width="1112" height="189" alt="image" src="https://github.com/user-attachments/assets/ad8c4815-5e3d-4c15-9ff4-c3b460433b6d" />

AWS will automatically create a tag with key Name and value Web Server 1.

Choose AMI
In the Amazon Machine Image (AMI) section, keep the default:Amazon Linux 2023 AMI

<img width="1217" height="369" alt="image" src="https://github.com/user-attachments/assets/948a99d7-2c9d-4088-bcc2-3ac3bdcfd803" />

Choose Instance Type

In the Instance type section select:t2.micro

<img width="1127" height="305" alt="image" src="https://github.com/user-attachments/assets/bb3d3027-87c4-41b4-8f05-c393d6286f39" />

This instance type is eligible for the AWS Free Tier and includes 1 vCPU and 1 GiB memory.

Select Key Pair

Under Key pair name, choose:vockey

<img width="1093" height="198" alt="image" src="https://github.com/user-attachments/assets/5ac3b710-ed72-41d5-b80b-5093c0becdd1" />

A key pair is required even if you won’t be connecting via SSH during this lab.

### Configure Network Settings

Click Edit next to Network settings, then set:

Network: `lab-vpc`

Subnet: `lab-subnet-public2`

Ensure this is a public subnet, not a private one.

Auto-assign public IP: Enable

Under Firewall (security groups):

Select Select existing security group

Choose:`Web Security Group`

<img width="1192" height="731" alt="image" src="https://github.com/user-attachments/assets/5f7c0a15-9fc5-4441-83aa-8af5798f496c" />

###  Configure Storage
In the Configure storage section, keep the default settings:

8 GiB General Purpose SSD (gp3)

### Add User Data Script

Expand the Advanced details panel at the bottom.

Scroll to User data and paste the following script

```bash
#!/bin/bash
# Install Apache Web Server and PHP
dnf install -y httpd wget php mariadb105-server
# Download Lab files
wget https://aws-tc-largeobjects.s3.us-west-2.amazonaws.com/CUR-TF-100-ACCLFO-2/2-lab2-vpc/s3/lab-app.zip
unzip lab-app.zip -d /var/www/html/
# Turn on web server
chkconfig httpd on
service httpd start
```

Scroll to the Summary panel on the right and click Launch instance.

You will see a Success message. Click View all instances.

<img width="1559" height="301" alt="image" src="https://github.com/user-attachments/assets/24785512-abd2-4ff6-a52d-95804943d04b" />

Wait for the instance named Web Server 1 to show:

Status check = 2/2 checks passed

### Test the Web Server

Select the instance Web Server 1.

In the Details tab, copy the Public IPv4 DNS.

<img width="1542" height="727" alt="image" src="https://github.com/user-attachments/assets/b07f53bc-bf2f-4fb4-a81e-996b0b22a190" />

Open a new browser tab, paste the DNS URL, and press Enter.

<img width="1825" height="579" alt="image" src="https://github.com/user-attachments/assets/899ff752-06f2-4cf2-b18e-644044e2f50d" />

You should see a web page displaying the AWS logo and instance meta-data values.

You can now submit your work and end the lab after that.


