#  Terraform for Beginners – Complete Visual & Practical Guide

This repository helps you understand Terraform concepts **theoretically and practically**.

---
#  Theoretically



##  What is Terraform?
Terraform is an **Infrastructure as Code (IaC)** tool by **HashiCorp** that allows you to define, provision, and manage infrastructure using declarative configuration files.



---

##  Why Terraform?
![Terraform Benefits](images/2.png)

### Key Benefits
- Infrastructure as Code
- Version Control
- Cloud Agnostic
- Safe execution using `terraform plan`

---

##  Infrastructure as Code (IaC)
![IaC Benefits](images/1.png)
![IaC Benefits](images/3.png)

IaC allows you to:
- Automate infrastructure
- Avoid configuration drift
- Reproduce environments easily

---

##  Terraform Workflow
![Terraform Workflow](images/4.png)


```bash
terraform init
terraform plan
terraform apply
terraform destroy
```

---

##  Terraform Configuration Files


Terraform uses .tf files written in HCL.

Example:

```hcl
resource "aws_instance" "web" {
  ami           = "ami-0abcd1234"
  instance_type = "t2.micro"
}
```

---

##  File Organization Best Practices

Recommended structure:

```
.
├── main.tf
├── variables.tf
├── outputs.tf
└── provider.tf
```

---

##  Terraform Core
![Terraform Core](images/5.png)
Terraform Core:

Reads configuration

Builds dependency graph

Communicates with providers

Manages state

---

##  Terraform Plugins
![Terraform Plugins](images/6.png)
Providers and provisioners are plugins.

---

##  Provider vs Provisioner
![Provider vs Provisioner](images/7.png)
Provider → manages APIs

Provisioner → executes scripts

---

## Terraform Files Recap
![Terraform Files Recap](images/8.png)
![Terraform Files Recap](images/9.png)

---

##  Terraform Workflow
![Terraform Workflow](images/10.png)
![Terraform Workflow](images/11.png)

---

##  Providers
![Providers](images/12.png)
![Providers](images/13.png)

---

##  Resources in Terraform
![Resources in Terraform](images/14.png)
![Resources in Terraform](images/15.png)
![Resources in Terraform](images/16.png)
Syntax:

```hcl
resource "<PROVIDER>_<TYPE>" "<NAME>" {}
```

Example:

```hcl
resource "aws_s3_bucket" "bucket" {
  bucket = "my-terraform-bucket"
}
```

---

## Resource Identification

Reference format:

```hcl
aws_s3_bucket.bucket.id
```

---

## Provider Authentication
![Provider Authentication](images/17.png)
Example:

```hcl
provider "aws" {
  region = "us-east-1"
}
```

---

##  Terraform Output
![Terraform Output](images/18.png)
![Terraform Output](images/19.png)
![Terraform Output](images/20.png)
![Terraform Output](images/21.png)
```hcl
output "instance_ip" {
  value = aws_instance.web.public_ip
}
```

```bash
terraform output
```

---

## Output Options

```hcl
output "password" {
  value     = var.db_password
  sensitive = true
}
```

---

## Output Dependencies

```hcl
output "lb_dns" {
  value      = aws_lb.main.dns_name
  depends_on = [aws_lb.main]
}
```

---



---

##  Resource Syntax
![Resource Syntax](images/22.png)
![Resource Syntax](images/23.png)

---

##  Terraform Settings
![Terraform Settings](images/24.png)

---

##  Init Phase
![Init Phase](images/25.png)


```bash
terraform init

```

---


##  Plan Phase
![Plan Phase](images/26.png)
```bash
terraform plan
terraform plan -out=tfplan
```

---

##  Saved Plan
![Saved Phase](images/27.png)
```bash
terraform apply tfplan
```

---

##  Apply Phase
![Apply Phase](images/28.png)
```bash
terraform apply
terraform apply -auto-approve
```

---

##  Destroy Phase
![Destroy Phase](images/29.png)
```bash
terraform destroy
terraform destroy -target=aws_instance.web
```

---

##  Terraform State

Terraform tracks infrastructure using state file.
![Terraform State](images/30.png)
![Terraform State](images/31.png)
![Terraform State](images/32.png)


---

##  Data Sources
![Data Sources](images/33.png)
```hcl
data "aws_region" "current" {}

output "region" {
  value = data.aws_region.current.name
}
```

---

##  Terraform Variables
![Terraform Variables](images/34.png)
![Terraform Variables](images/35.png)
![Terraform Variables](images/36.png)
![Terraform Variables](images/37.png)
```hcl
variable "instance_type" {
  default = "t2.micro"
}
```

---

## Variable Data Types
![Variable Data Types](images/38.png)
![Variable Data Types](images/39.png)
![Variable Data Types](images/40.png)

```hcl
variable "azs" {
  type = list(string)
}
```



---

##  Variables Example

```hcl
cidr_blocks = [var.vpn_ip]
```

---

##  Variable Assignment Methods

```bash
export TF_VAR_image_id=ami-123
terraform apply -var="image_id=ami-123"
terraform apply -var-file="prod.tfvars"
```

---

##  Terraform Registry Modules
![Terraform Registry Modules](images/42.png)
![Terraform Registry Modules](images/43.png)
![Terraform Registry Modules](images/44.png)
```hcl
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "5.0.0"
}
```

---

##  Verified Modules

Format:

```
namespace/name/provider
```

---

##  Local Modules
![Local Modules](images/41.png)
```hcl
module "ec2" {
  source = "./modules/ec2"
}
```

---

##  Terraform Registry

🔗 https://registry.terraform.io/






---
# Practical With AWS 
# Terraform AWS Infrastructure Deployment

This Terraform configuration deploys a complete AWS infrastructure including VPC, networking components, security groups, and EC2 instances with EBS volumes. The configuration is modular and uses variables for customization.

---

## Table of Contents


# Terraform AWS Infrastructure Example

This README provides a step-by-step guide and code samples for provisioning AWS infrastructure using Terraform. It covers VPC, subnets, route tables, security groups, EC2, and more.

---

## Concepts Mapping

- **provider**: like importing a library
- **resource/data**: like calling a function from the library
- **arguments**: parameters to the function

---

## Example: Create VPC and Subnets

```hcl
resource "aws_vpc" "development-vpc" {
  cidr_block = "10.0.0.0/16"
}

resource "aws_subnet" "dev-subnet-1" {
  vpc_id            = aws_vpc.development-vpc.id
  cidr_block        = "10.0.0.0/16"
  availability_zone = "us-east-1a"
  tags = {
    Name = "subnet-1-dev"
  }
}
```

### Get Existing VPC
```hcl
data "aws_vpc" "existing_vpc" {
  default = true
}
```

### Create Subnet in Existing VPC
```hcl
resource "aws_subnet" "dev-subnet-2" {
  vpc_id            = data.aws_vpc.existing_vpc.id
  cidr_block        = "172.31.48.0/20"
  availability_zone = "us-east-1a"
  tags = {
    Name = "subnet-2-default"
  }
}
```

---

## Terraform State

- List resources: `terraform state list`
- Show resource: `terraform state show aws_subnet.dev-subnet-1`

---

## Outputs
```hcl
output "dev-vpc-id" {
  value = aws_vpc.development-vpc.id
}

output "dev-subnet-id" {
  value = aws_subnet.dev-subnet-1.id
}
```

---

## Variables

1. If you don't provide a value, Terraform will prompt during apply.
2. Use CLI: `terraform apply -var "subnet_cidr_block=10.0.1.0/24"` or `terraform apply -var-file=terraform-dev.tfvars`
3. Use `terraform.tfvars` file.
4. Or set env var: `export TF_VAR_avail_zone="us-east-1a"`

---

## Step-by-Step Infrastructure

### 1. Provider
```hcl
provider "aws" {
  region = "us-east-1"
}
```

### 2. Create VPC (with variables)
```hcl
resource "aws_vpc" "myapp-vpc" {
  cidr_block = var.vpc_cidr_block
  tags = {
    Name = "${var.env_prefix}-vpc"
  }
}
```

### 3. Create Subnet
```hcl
resource "aws_subnet" "myapp-subnet-1" {
  vpc_id            = aws_vpc.myapp-vpc.id
  cidr_block        = var.subnet_cidr_block
  availability_zone = var.avail_zone
  tags = {
    Name = "${var.env_prefix}-subnet-1"
  }
}
```

### 4. Create Internet Gateway
```hcl
resource "aws_internet_gateway" "myapp-igw" {
  vpc_id = aws_vpc.myapp-vpc.id
  tags = {
    Name = "${var.env_prefix}-igw"
  }
}
```

### 5. Create Route Table
```hcl
resource "aws_route_table" "myapp-route-table" {
  vpc_id = aws_vpc.myapp-vpc.id
  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.myapp-igw.id
  }
  tags = {
    Name = "${var.env_prefix}-rtb"
  }
}
```

### 6. Associate Route Table with Subnet
```hcl
resource "aws_route_table_association" "a-rtb-subnet" {
  subnet_id      = aws_subnet.myapp-subnet-1.id
  route_table_id = aws_route_table.myapp-route-table.id
}
```

### 7. Security Group (allow all in/out)
```hcl
resource "aws_security_group" "myapp-sg" {
  name   = "myapp-sg"
  vpc_id = aws_vpc.myapp-vpc.id
  ingress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
  tags = {
    Name = "${var.env_prefix}-sg"
  }
}
```

---

## (Optional) Use Default Security Group
```hcl
resource "aws_default_security_group" "default-sg" {
  vpc_id = aws_vpc.myapp-vpc.id
  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = [var.my_ip]
  }
  ingress {
    from_port   = 8080
    to_port     = 8080
    protocol    = "tcp"
    cidr_blocks = [var.my_ip]
  }
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = [var.my_ip]
  }
  tags = {
    Name = "${var.env_prefix}-default-sg"
  }
}
```

---

## 8. (Optional) Get Latest Amazon Linux AMI
```hcl
data "aws_ami" "latest-amazon-linux-image" {
  most_recent = true
  owners      = ["amazon"]
  filter {
    name   = "name"
    values = ["amzn2-ami-kernel-*-x86_64-gp2"]
  }
}

output "aws_ami_id" {
  value = data.aws_ami.latest-amazon-linux-image.id
}
```

---

## 9. Create SSH Key Pair
```hcl
resource "aws_key_pair" "ssh-key" {
  key_name   = "fromTerraform"
  public_key = file(var.public_key_location)
}
```

---

## 10. Launch EC2 and Run Docker Container
```hcl
resource "aws_instance" "myapp-server" {
  ami                         = "ami-0fe630eb857a6ec83" # RedHat 8
  instance_type               = var.instance_type
  subnet_id                   = aws_subnet.myapp-subnet-1.id
  vpc_security_group_ids      = [aws_security_group.myapp-sg.id]
  availability_zone           = var.avail_zone
  associate_public_ip_address = true
  key_name                    = aws_key_pair.ssh-key.key_name
  user_data                   = file("jenkins-rhel.sh")
  tags = {
    Name = "${var.env_prefix}-server"
  }
}
```

---

## 11. Attach EBS Volume
```hcl
resource "aws_ebs_volume" "example" {
  availability_zone = "us-east-1a"
  size              = 30
}

resource "aws_volume_attachment" "example" {
  device_name = "/dev/sdf"
  instance_id = aws_instance.myapp-server.id
  volume_id   = aws_ebs_volume.example.id
}
```

---

## 12. Output EC2 Public IP
```hcl
output "ec2_public_ips" {
  value = aws_instance.myapp-server.public_ip
}
```

---

## Typical Steps

1. Create the VPC
2. Create the subnet from the VPC
3. Create route table and internet gateway
4. Provision EC2
5. Deploy Nginx Docker container
6. Create security group

---

## Notes

- Use variables for flexibility and reusability.
- Use `terraform plan` to preview changes.
- Use `terraform apply` to provision resources.
- Use `terraform destroy` to remove resources.

---

**End of README**
---

## Usage Example

```bash
# Initialize Terraform
terraform init

# Plan deployment
terraform plan -var-file=production.tfvars

# Apply configuration
terraform apply -var-file=production.tfvars

# Destroy infrastructure
terraform destroy -var-file=production.tfvars
```

---

This configuration provides a complete infrastructure-as-code solution for deploying a web application on AWS with proper networking, security, and compute resources.


---

##  Final Tips
Always use terraform plan

Store state remotely for teams

Use modules for reuse

Never commit secrets

---

## 👤 Author
Abdullah Mohamed Abdelbadea
