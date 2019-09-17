# Terraform course

### What
 - Infrastruture as code.
 - Automation of your infrastruture.
 - Keep your infrastruture state(compilant).
 - Make your infrastruture **auditable**, ex.: over GIT.

### Installing
 - Go to [terraform.io](https://www.terraform.io/) and download based on your SO.
 - Add in your PATH variable where youd unzipped the previous downloaded file. Ex.: `export PATH={user.home}/tools/terraform`. After type `terraform help`.

### First steps
- Spining up an EC2 instance
	- Create IAM admin user
	- Create terraform file to spin up `t2.micro` instante.
	- Run terraform apply.

- Create a user called `terraform`, then save the private key and add an `AdministrationAccess` on `Permissions` tab.

### Create a terraform file
Create a file called `instance.tf` like:
```terraform
provider "aws" {
  access_key = "ACCESS_KEY_HERE"
  secret_key = "SECRET_KEY_HERE"
  region     = "us-east-1"
}

resource "aws_instance" "example" {
  ami           = "ami-0d729a60"
  instance_type = "t2.micro"
}
```

For the `ami` image, search in http://cloud-images.ubuntu.com/locator/ec2/ and find for your region.

The first command to execute is `terraform init` and then `terraform apply`. After to execute you can undo the apply command just `terraform destroy`. 

Another interesting command is `terraform plan` which it'll show you what the terraform is planning to do, before really applying it.

## Variables
Use variable to hide secrets and elements that **might change**.

Let's split the `instance.tf` file put into `provider.tf`:
```terraform
provider "aws" {
  access_key = var.AWS_ACCESS_KEY
  secret_key = var.AWS_SECRET_KEY
  region     = var.AWS_REGION
}
```
and create a file `vars.tf` like:
```terraform
variable "AWS_ACCESS_KEY" {}
variable "AWS_SECRET_KEY" {}
variable "AWS_REGION_KEY" {
	default = 'region variable value'
}
variable "AMIS" {
  type = map(string)
  default = {
    us-east-1 = "ami-13be557e"
    us-west-2 = "ami-06b94666"
    eu-west-1 = "ami-0d729a60"
  }
}
```
and then create `terraform.tfvars` with values of variables:
```terraform
AWS_ACCESS_KEY=""
AWS_SECRET_KEY=""
AWS_REGION=""
```
**Warning**
This file must be defined in `.gitignore` file, to not expose the secrets in the repository.

The `instance.tf` end up like:
```terraform
resource "aws_instance" "example" {
  ami           = var.AMIS[var.AWS_REGION]
  instance_type = "t2.micro"
}
```
