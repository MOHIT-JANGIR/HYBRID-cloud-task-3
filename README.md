![image](https://user-images.githubusercontent.com/61896468/91653525-61484200-eabf-11ea-8cbe-fdb61a84f94d.png)
Task Description

We have to create a web portal for our company with all the security as much as possible.

So, we use Wordpress software with dedicated database server.

Database should not be accessible from the outside world for security purposes.

We only need to public the WordPress to clients.

So here are the steps for proper understanding!

Steps:

1) Write a Infrastructure as code using terraform, which automatically create a VPC.

2) In that VPC we have to create 2 subnets:

  a) public subnet [ Accessible for Public World! ] 

  b) private subnet [ Restricted for Public World! ]

3) Create a public facing internet gateway for connect our VPC/Network to the internet world and attach this gateway to our VPC.

4) Create a routing table for Internet gateway so that instance can connect to outside world, update and associate it with public subnet.

5) Launch an ec2 instance which has Wordpress setup already having the security group allowing port 80 so that our client can connect to our wordpress site.

Also attach the key to instance for further login into it.

6) Launch an ec2 instance which has MYSQL setup already with security group allowing port 3306 in private subnet so that our wordpress vm can connect with the same.

Also attach the key with the same.

Note: Wordpress instance has to be part of public subnet so that our client can connect our site. 

mysql instance has to be part of private subnet so that outside world can't connect to it.

Don't forgot to add auto ip assign and auto dns name assignment option to be enabled.

Prerequisites -:

AWS Console Account

AWS CLI

Terraform Installed

Putty or Mobaxterm

# Steps to perform this task -:

# Step 1: Defining the provider on which we have to create infrastructure. In our case is AWS.

```
provider "aws" {

region = “ap-south-1”

profile = “default”

}
```

# Step 2: Creating a VPC with CIDR Block 192.168.0.0/16 and enable DNS Hostnames to assign dns names to instances.

```
resource “aws_vpc” “myvpc”{

cidr_block = “192.168.0.0/16”

instance_tenancy = “default”

enable_dns_hostnames = true

tags = {

Name = “anivpc”

}

}
```

# Step 3: Creating public subnet-:

```
resource "aws_subnet" "public" {

vpc_id = aws_vpc.vpc.id

cidr_block = "192.168.10.0/24"

availability_zone = "ap-south-1b"

map_public_ip_on_launch = "true"

tags = {

Name = "public-subnet"

}

}
```

# Step 4: Creating a private subnet-:

```
resource "aws_subnet" "private" {

vpc_id = aws_vpc.vpc.id

cidr_block = "192.168.20.0/24"

availability_zone = "ap-south-1a"

tags = {

Name = "private-subnet"

}

}
```

# Step 5: Creating a Internet Gateway in Previosly created VPC for PUBLIC SUBNET to connect Internet.

```
resource "aws_internet_gateway" "gateway" {

vpc_id = aws_vpc.myvpc.id

tags = {

Name = "ani_gateway"

}

}
```

# Step 6: Write a Terraform code to Create a routing table for Internet gateway so that instance can connect to outside world, update and associate it with public subnet.

```
resource "aws_route_table" "route" {

vpc_id = aws_vpc.vpc.id

route {

cidr_block = "0.0.0.0/0"

gateway_id = aws_internet_gateway.gateway.id

}

tags = {

Name = "gatewayroute"

}

}

resource "aws_route_table_association" "public"

{

subnet_id = aws_subnet.public.id

route_table_id = aws_route_table.route.id

}
```

# Step 7: Creating a Security Group which allows port 80 for http and 22 for ssh in inbound rule and allows all port in outbound rule.

```
resource "aws_security_group" "tsk3" {

name = "task3"

description = "Allow inbound traffic"

vpc_id = aws_vpc.myvpc.id

ingress {

from_port = 80

to_port = 80

protocol = "tcp"

cidr_blocks = ["0.0.0.0/0"]

}

ingress {

from_port = 22

to_port = 22

protocol = "tcp"

cidr_blocks = ["0.0.0.0/0"]

}

egress {

from_port = 0

to_port = 0

protocol = "-1"

cidr_blocks = ["0.0.0.0/0"]

}

tags = {

Name = "task3"

}

}
```

# STEP 8: Write a terraform code to Launch an ec2 instance which has Wordpress setup already having the security group allowing port 80 so that our client can connect to our wordpress site.

```
resource "aws_instance" "wordpress" {

ami = "ami-004a955bfb611bf13"

instance_type = "t2.micro"

associate_public_ip_address = true

subnet_id = aws_subnet.public.id

vpc_security_group_ids = [ aws_security_group.task3.id]

key_name = "aniket1234"

tags = {

Name = "anios"

}

}
```

# To use our terraform code first we have to initialize it by using this command-:

```
terraform init
```
# after that we have to run this command and the terraform will perform the task.

```
terraform apply --auto-approve
```

# Few Screenshots of the task are as follows-:

![image](https://user-images.githubusercontent.com/61896468/98758990-110e2a00-23f6-11eb-8d30-ef321cf0719d.png)
![image](https://user-images.githubusercontent.com/61896468/91653576-b97f4400-eabf-11ea-8a7e-9abf8d1109de.png)
![image](https://user-images.githubusercontent.com/61896468/91653578-c0a65200-eabf-11ea-9883-36b6747445f6.png)
![image](https://user-images.githubusercontent.com/61896468/91653582-c865f680-eabf-11ea-83d1-dc4f2d1e3684.png)
![image](https://user-images.githubusercontent.com/61896468/91653589-d0259b00-eabf-11ea-9ba3-e04d7157be7f.png)
![image](https://user-images.githubusercontent.com/61896468/91653599-d9af0300-eabf-11ea-9160-e5f86139e2a0.png)

# Thanks for reading


![image](https://user-images.githubusercontent.com/61896468/91653508-3fe75600-eabf-11ea-9e48-5b62d63a99c2.png)

 # THANK YOU DEDICATED VIMAL SIR AND ALL 24*7 WORKING VOLUNTEERS FOR ALL YOUR VALUABLE SUPPORT AND HELP.... 
