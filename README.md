## Install AWS CLI
```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```
## AWS Configure
```
WS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
Default region name [None]: us-west-1
Default output format [None]: json

aws configure list

AWS Academy: add token in the ~/.aws/credentials
```

## Install Terraform
```
curl https://releases.hashicorp.com/terraform/1.0.5/terraform_1.0.5_linux_amd64.zip --output terraform_1.0.5_linux_amd64.zip
unzip terraform_1.0.5_linux_amd64.zip
sudo mv terraform /usr/local/bin/terraform
terraform --version
```
## Creating EC2
```
export AWS_PROFILE=default
create-ec2.tf

provider "aws" {
  region = "us-east-1"
}

resource "tls_private_key" "private_key" {
  algorithm = "RSA"
  rsa_bits  = 4096
}

resource "aws_key_pair" "generated_key" {
  key_name   = "ride-share-key"
  public_key = tls_private_key.private_key.public_key_openssh
}

resource "aws_security_group" "security_group" {
  name        = "ride-sharing-sg"
  description = "Allow SSH inbound traffic"

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

resource "aws_instance" "ride_share_instance" {
  ami           = "ami-14c5486b"
  instance_type = "t2.micro"
  key_name = aws_key_pair.generated_key.key_name

  tags = {
    Name = "ride-share"
  }

  vpc_security_group_ids = [aws_security_group.security_group.id]
}

output "private_key" {
  value     = tls_private_key.private_key.private_key_pem
  sensitive = true
}

terraform init
terraform apply
```
## Updating EC2
```
provider "aws" {
  region = "us-east-1"
}

resource "tls_private_key" "private_key" {
  algorithm = "RSA"
  rsa_bits  = 4096
}

resource "aws_key_pair" "generated_key" {
  key_name   = "ride-share-key"
  public_key = tls_private_key.private_key.public_key_openssh
}

resource "aws_security_group" "security_group" {
  name        = "ride-sharing-sg"
  description = "Allow SSH inbound traffic"

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

resource "aws_instance" "ride_share_instance" {
  ami           = "ami-14c5486b"
  instance_type = "t3.micro"  # CHANGED HERE
  key_name = aws_key_pair.generated_key.key_name

  tags = {
    Name = "ride-share"
  }

  vpc_security_group_ids = [aws_security_group.security_group.id]
}

output "private_key" {
  value     = tls_private_key.private_key.private_key_pem
  sensitive = true
}

terraform apply
```
## Destroy
```
terraform destroy
```
