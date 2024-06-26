Types of IAC Tools
------------------

**1. Configuration Management:**

  - Ansible
  - Puppet
  - Saltstack
  - Chef

          These are commonly used to manage and istall softwares on already existing infrastructure resources such as DB,n/w,or even application configuration.
          Maintains standard structure
          Version Control
          Idempotent --> This means you can run code multiple times, it will only make changes that are necessary to bring the environment into a defined state.
          It would leave anything already in place as it is, without us having to write any additional code.

**2. Server Temaplating Tools**

  - Docker
  - Packer
  - Hashocorp Vagrant

          The most common examples for server templated images are VM images such as those that are offered on osboxes.org, custom AMIs in Amazon AWS, and Docker images on DockerHub and other container registries. 
          Server templating tools also promote immutable infrastructure unlike configuration management tools. This means that, once the VM or a container is deployed, it is designed to remain unchanged.
          If there are changes to be made to the image, instead of updating the running instance, like in the case of configuration management tools such as Ansible,
          we update the image and then re-deploy a new instance using the updated image.

**3. Provisioning Tool**

  - Hashicorp Terraform
  - Cloudformation

          Deploy Immutable Infrastructure Resoruces - These tools are used to provision infrastructure components using a simple declarative code.
          These infrastructure components can range from servers such virtual machines, databases, VPCs, subnets,security groups, storage, and just about any services based on the provider we choose.
          Supports multiple providers.

Why Terrafrom?
--------------

TF supports multiple clouds/onpremises using providers. 
It is written in Hashicorp Configuration Language (HCL) which is declarative in nature.

Declarative means whatever state we wanted we defined it in .tf file and terraform will brings the infrastructure in that state. We dont have to worry about it.
Terraform does it in 3 phases.

init : In it phase terraform initialises the project and identifies the providers used for the targe environment .
plan : During the plan phase terrafrom drafts a plan to get to the target stage.
apply : In apply phase tf makes the changes in target environment to bringe it to the desired stage.

Every object that tf manages is called as resource. A resource can be a compute instance, a database server in the cloud, or in a physical server on-premise that Terraform manages.
Terraform manages the lifecycle of the resources from its provisioning to configuration to decommissioning.
Terraform records the state of the infrastructure as it is seen in the real world and based on this,
it can determine what actions to take when updating resources for a particular platform.
Terraform can ensure that the entire infrastructure is always in the defined state at all times.

Reource is object that terrafrom can manage. It can be anything a file present in local of any object like ec2,vpc,eks,gke,azure active directory etc.

Types of files:
--------------
1. main.tf         --> Main configuration file containing resource defination.
2. variables.tf    --> Contains variable declaration
3. output.tf       --> Contains output from resources
4. provider.tf     --> Contains provider definition

3 Types of providers:
---------------------

**Official:** Official providers are owned and maintained by HashiCorp	hashicorp

**Partner:** Partner providers are written, maintained, validated and published by third-party companies against their own APIs. 
To earn a partner provider badge the partner must participate in the HashiCorp Technology Partner Program.	Third-party organization, e.g. mongodb/mongodbatlas

**Community:** Community providers are published to the Terraform Registry by individual maintainers, groups of maintainers, or other members of the Terraform community.	
Maintainer’s individual or organization account, e.g. DeviaVir/gsuite

Multiple Providers:
-------------------
ex. main.tf

        resource "local_file" "pet_name" {
        	    content = "We love pets!"
        	    filename = "/root/pets.txt"
        }
        
        resource "random_pet" "my-pet" {
        	      prefix = "Mrs"
        	      separator = "."
        	      length = "1"
        }
        
        resource "aws_instance" "ec2_instance" {
        	  ami       =  "ami-0eda277a0b884c5ab" 
        	  instance_type = "t2.large"
        }
        
        resource "aws_ebs_volume" "ec2_volume" {
        	  availability_zone = "eu-west-1"
        	  size  =    10
        }

        resource "kubernetes_namespace" "dev" {
          metadata {
            name = "development"
        }

here, random --> provider
      pet --> resoruce_type

terraform init --> will install two providers plugin local_provider, random_provider and aws_provider

Mrs.hen --> will be created.

variables.tf
------------

To refer variable in main.tf use var.variable name without "".

      variables.tf
      
      variable "filename" {
          defaults = "/root/pets.txt"
          type = string                         //optional
          description = The path of local file" //optional
      }
      variable "content" {
          defaults = "We love pets!"
      }

      main.tf
      
      resource "local_file" "pet_name" {
        	content = var.content  // To refer variable in main.tf use var.variable name without ""
        	filename = var.filename
      }

variable type:
-------------
- string --> "/root/pet.txt" (alphanumeric)
- number --> 1               (number)
- bool   --> true/false      (boolean)
- any    --> default value   (by default)
- list   --> ["cat","dog"]   [numberd collection start with position 0,1,2..]

      variable "gender" {
           type = list(string)
           default = ["Male", "Female"]
      }

      resource "random_pet" "mypet" {
            gender = var.genger[0]
      }

- map    -->  pet1 = cat
              pet2 = dog

      variable "jedi" {
           type = map
           default = {
               filename = "/root/first-jedi"
               content = "phanius"
           }
      }

      main.tf

      resource "local_file" "pet_name" {
        	content = var.jedi["content"]
        	filename = var.jedi["filename"]
      }

- object --> Complex data structure

      variable "bella" {
        type = object({
            name = string
            age = number
            color = string
            food = list(string)
            favourite_pet = bool
        })
    
        default = {
              name = "bella"
              color = "brown"
              age = 7
              food = ["fish","meat"]
              favourite_pet = true
        }    
      }

- set    --> list of items without duplicates

    variable "users" {
         type = set(string)
         default = ["tom", "jerry", "pluto", "daffy", "donald", "chip", "dale"]  
    }

    variable "age" {
        aged = [ 5,7,10 ]
        type = set(number)
  }

- tuple  --> tuple is same like list. In list we can have elements of same types where as in tuple we can have lements of different types(strings,numbers,bool etc)

      varable "kitty" {
          type = tuple([string,bool,number])
          default = ["cat",true,7]
      }

**We can pass the values to the terrafrom at runtime as well:**

    terraform apply -var "filename=/root/pets.txt" -var "content=this is sample one" -var "prefix=Mr."

    or

    export TF_VAR_filename="/root/pets.txt" 
    export TF_VAR_content="this is sample one"
    export TF_VAR_prefix="Mr."
    terraform apply

    or

    terrafrom.tfvars
    ---------------
    filename="/root/pets.txt" 
    content="this is sample one"
    prefix="Mr."    

    $ terrafrom apply

    or

    if we have variable file as variable.tfvars then we have to pass it manually like

    $ terraform apply -var-file variable.tfvars

    Automatically loaded :
    
    - terraform.tfvar | terraform.tfvars.json
    - *.auto.tfvars   | *.auto.tfvars.json
    
variable Definition Precedence 
------------------------------
1. Envirnment variables                  --> Low Priority
2. terrafrom.tfvars
3. *.auto.tfvars (alaphabetic order)
4. -var or -var-file (comman-line-flags)  --> High proirity)

Resoruce Attribute
------------------
Suppose if we want to use the value of one resource in another resoruce we can use it, we have to follow the syntax:

        ${resource.resrouce_name.attribute}  --> interpolation sequence 

        resource "random_pet" "pets" {
          prefix = var.prefix
          seperator = var.seperator
          length = var.length
        }

        resoruce "local_file" "my-file"{
          filename = var.filename
          contenxt = "My favourite pet is ${random_pet.pets.id}"
        }

        or

        resource "tls_private_key" "pvtkey" {
            algorithm = "RSA"
            rsa_bits = 4096 
        }
        
        resource "local_file" "key_deatils" {
            filename = "/root/key.txt"
            content = "${tls_private_key.pvtkey.private_key_pem}"
        }

Resource Dependency 
-------------------
By default terraform will identify resource dependency (implicit dependency) if we use reference expression. If we want to specify dependency we can do it using depends on clause explicitly.

        resource "random_pet" "pets" {
          prefix = var.prefix
          seperator = var.seperator
          length = var.length
        }

        resoruce "local_file" "my-file"{
          filename = var.filename
          contenxt = "My favourite pet is Tom"
          depends_on = [
              random_pet.pets
          ]
        }

Output variables
----------------
This variables used to store value of expression in terrafrom. Syntax use to store this is:

        resource "random_pet" "pets" {
          prefix = var.prefix
          seperator = var.seperator
          length = var.length
        }

        resoruce "local_file" "my-file"{
          filename = var.filename
          contenxt = "My favourite pet is ${random_pet.pets.id}"
        }

        output pet-name {
          value = random_pet.pets.id
          description = "Record the value of pet ID generated bu random_pet resoruce"
        }

        Syntax:

        output "<variable_name>" {
          value = "<variable_value>"
          <arguments>
        }

Terraform State
----------------

Terrafrom uses state file to map the resources configuration to the real world object. State file is considered as blueprint of all the resoruce that TF manages in real world.

Terrafrom State will be useful :
- To find resource dependency
- To maintain the resoruce state

State is a non-optional feature in Terraform.However, there are a few considerations. First one is that, the state file contains sensitive information. Within it, it contains every little detail about our infrastructure.
When working as a team,it is considered a best practice to store Terraform configuration files in distributed version control systems,such as GitHub,GitLab, or Bitbucket.
However, owing to the sensitive nature of the state file,it is not recommended to store them in Git repositories.
Instead, store the state in remote backend systems such as AWS S3, Google Cloud Storage, Azure Storage, Terraform Cloud, etc.
Terraform state is a JSON data structure that is meant for internal use within Terraform. We should never manually attempt to edit the state files ourselves. 
However, there would be situations where we may want to make changes to the state file. And in such cases, we should rely on terraform state commands.


Terraform Commands
------------------
- terrafrom validate --> to check if configuration is valid.
- terraform fmt or terraform format --> To format configuration in more readable canonical form.
- terraform show --> show the current of the resource that TF sees. 
- terraform show -json
- terraform providers --> to see all providers
- terraform providers mirror /root/new_path --> this will mirror the provider plugins to new path.
- terraform output <variable_name>
- terraform refresh --> To refresh TF state file with realworkld infrastructures. This will not modify any infrastructure but it will modify the state file. This is also run auto by terraform plan and apply.
- terraform graph

Lifecycle Rules
-------------------------------------
By default Terraform will first deleted old resoruce and then create new resource.
- create_before_destroy : This will create new resoruce first and then delete the older one.
- prevent_destroy : This will prevent the resoruce from deleted. The resrouce will be still deleted if we run terrafrom destroy command. This will only prevent if it will be deleted in subsequent apply.
- ignore_changes : This rule when applied will prevent a resoruce from being updated based on a list of attributes that we define within lifecycle block.


      ex.1
      resoruce "local_file" "sample" {
        filename = "/root/sample.txt"
        content  = "This is the sample file"
        file_permission = "0700"
      
        lifecycle {
          create_before_destroy = true
        }
      }

      ex.2 
      resoruce "aws_instance" "webserver" {
        ami           = "ami-12345"
        instance_type = "t2.lasrge"
        tags = {
            name = "my-webserver"
        }
      lifecycle {
          ignore_chanes = [
              tags,ami
          ]
      }
      }

Datasources:
------------
Data sources allow Terraform to read attributes from resources which are provisioned outside its control. Datasoruce only reads infrastructure.
The data block is quite similar to the resource block. Instead of the keyword called resource, we define a data source block with the keyword called "data."
This is followed by the type of resource which we are trying to read.

suppose we have a local file created with shell as dogs.txt.The data read from a data source is then available under the "data object" in Terraform.
So, To use this data in the resource called "Pet", we could simply use data.local_file.dog.content.


      cat /root/dogs.txt
      Dogs are awesome.
      
      resoruce "local_file" "pet" {
        filename = "/root/pets.txt"
        content  = data.local_file.dog.content
      }
      
      data "local_file" "dog" {
        filename = "/root/dogs.txt"
      }

Meta Arguments
--------------
Meta arguments can be used within any resoruce block. We have already seen two of them
- depends_on 
- lifecycle
- count : One of easiest way to create multiple instances of resoruce is to use count meta-argument. When we use count resources are created as list.

      ex.
      
      variables.tf
      
      variable "filename" {
        default = [
          "/root/dogs.txt",
          "/root/cats.txt",
          "/root/cows.txt",
          "/root/pigs.txt"
        ]
      }
      
      resoruce "local_file" "pets" {
        filename = var.filename[count.index]
      
        count = length(var.filename)
      }

- for_each : for_each will work only with map or sets.

      ex.
      
      variables.tf
      ---
      variable "filename" {
      type=list(string)
        default = [
          "/root/dogs.txt",
          "/root/cats.txt",
          "/root/cows.txt",
          "/root/pigs.txt"
        ]
      }
      
      resoruce "local_file" "pets" {
        filename = each.value
      
        for_each = toset(var.filename)
      }

AWS
---------
aws --endpoint http://aws:4566 iam create-user --user-name  mary

aws --endpoint http://aws:4566 iam attach-user-policy --policy-arn "arn:aws:iam::aws:policy/AdministratorAccess" --user-name  mary

aws iam --endpoint http://aws:4566 create-group --group-name project-sapphire-developers

aws iam --endpoint http://aws:4566 add-user-to-group --group-name project-sapphire-developerss --user-name jack

aws iam --endpoint http://aws:4566 list-attached-user-policies --user-name jack

aws iam --endpoint http://aws:4566 list-attached-group-policies --group-name project-sapphire-developers

aws iam --endpoint http://aws:4566 attach-group-policy --policy-arn arn:aws:iam::aws:policy/AmazonEC2FullAccess --group-name project-sapphire-developers


Attaching AWS policy doc:
--------------------------
      admin-policy.json
      {
          "version" : "2012-10-17",
          "Statement": [
            {
              "Effect": "Allow",
              "Action": "*",
              "Resoruce": "*"
            }
          ] 
      }
      
      main.tf
      resoruce "aws_iam_user" "admin-user" {
        name = "lucy"
        tags = {
          Description = "Technical Team Leader"
        }
      }
      
      resrouce "aws_iam_policy" "adminuser" {
        Name = "AdminUsers"
        policy = file("admin-policy.json")
      }
      
      or
      
      resrouce "aws_iam_policy" "adminuser" {
        Name = "AdminUsers"
        policy = >>EOF
        {
          "version" : "2012-10-17",
          "Statement": [
            {
              "Effect": "Allow",
              "Action": "*",
              "Resoruce": "*"
            }
          ] 
        }
        EOF
      }
      
      resource "aws_iam_user_policy_attachement" "lucy-admin-access" {
        user = aws_iam_user.admin-user.name
        policy_arn = aws_iam_user_policy_attachment.adminuser.arn
      }

Terrafrom State File
--------------------
    terraform {
      backend "s3" {
        key = "terraform.tfstate"
        region = "us-east-1"
        bucket = "remote-state"
      }
    }

terrafrom state <list|show|mv|pull|rm>

terrafrom state <subcommand> [options] [args]

ex. terrafrom state show aws_s3_bucket.finance

1. terrafrom state list [options] [address]

This will list all the resources recorded in the statefile. It will only print the resoruce address.

2. terrafrom state show  [options] [address]

This will show all the attributes of the reource.

ex. terrafrom state show aws_s3_bucket.finance

3. terrafrom state mv [options] source destination

This command is use to move items in different state file. The items can be moved in a same state file means moving resource from its current source address to another, means renaming resource.
Or it can move itsms from one state file to another.

ex. terrafrom state move aws_dynamodb_table.orig aws_dynamodb_table.original

4. terrafrom state pull

To pull the state file from remote.

5. terrafrom state rm

This is used to remove resoruce from state file.

Terrafrom Provisioner:
---------------------
To execute file on remote resource.


    provisioner "remote-exec" {
      inline = [ "sudo apt update",
                 "sudo apt install nginx -y"
              ]
    }

Local-exec use to run the command from where you are running the terrafrom apply command.

    provisioner "local-exec" {
      on_failure = fail|continue
      command = "echo ${aws_instance.webserver.public_ip} >> /tmp/ips.txt"
    }

By default provisioners are run after resources are created.

Destroy-time provisioner:

    provisioner "local-exec" {
      when = destroy
      command = "echo ${aws_instance.webserver.public_ip} >> /tmp/ips.txt"
    }

Ex.

      main.tf
      variable "ami" {
        default = "ami-06178cf087598769c"
      }
      
      variable "instance_type" {
        default = "m5.large"
      }
      
      variable "region" {
        default = "eu-west-2"
      }
      
      resource "aws_instance" "cerberus" {
        ami           = var.ami
        instance_type = var.instance_type
        key_name = "cerberus"

        user_data = file("install-nginx.sh")
      }

      resource "aws_key_pair" "cerberus-key" {
        key_name = "cerberus"
        public_key = file(".ssh/cerberus.pub")
      }
    
      resource "aws_eip" "eip" {
        vpc = true
        instance = aws_instance.cerberus.id
        provisioner "local-exec" {
          command = "echo ${aws_eip.eip.public_dns} >> /root/cerberus_public_dns.txt"
        }
    }
      
      provider.tf
      terraform {
        required_providers {
          aws = {
            source = "hashicorp/aws"
            version = "4.15.0"
          }
        }
      }

Terraform taint and untaint 
---------------------------

terraform untaint resrouce_name

Terrafrom logging
-----------------

Terrafrom provides 5 levels of logging. 

export TF_LOG=TRACE
export TF_LOG_PATH=/tmp/terraform.log

- INFO
- WARNING
- ERROR
- DEBUG
- TRACE

Terraform IMPORT
----------------
    To import existing infrastructrure resoruce in Terraform.
    
    Syntax: terrafrom import <resrouce_type>.<resoruce_name> <attribute>
    
    ex. terrafrom import aws_instance.webserver-2 i-026abcd9885
    
    Error: resource address "aws_instance.webserver-2" does not exists.
    
    Before importing this resource, please create its configuration in the root module.
    ex. 
    resoruce "aws_instance" "webserver-2" {
    # {resoruce_args}
    }

    Once this is done, the resoruce info will be added to the state file, but you need to manually then add it to the resoruce_block.
    After this run terrafrom plan, tf will understand resoruce is already there and will carry no action. Resrouce is not under the control of terrafrom.

Terrafrom Modules:
------------------
- Complex Configuration Files
- Duplicate Code
- Increased Risk
- Limits Reusabolity

Any configuration directry containing set of configuration files is called module.

to use module :

    mkdir -p terraform-projects/modules/payroll-app
    # app_server.tf
    ---
    resrouce "aws_instance" "app_server" {
      ami = var.ami
      instance_type = "t2.medium"
      tags = {
        Name = "${var.app_region}-app-server"
      }
      depends_on = [ aws_dynamodb_table.payroll_db,
                     aws_s3_bucket.payroll_data
                   ]
    }
    # dynamodb_table.tf
    ---
    resoruce "aws_dynamodb_table" "payroll_db" {
      name = "user_data"
      billing_mode = "PAY_PER_REQUEST"
      hash_key = "Emp_ID"
    
      attribute {
        name = "Emp_ID"
        type = "N"
      }
    }
    # s3_bucket.tf
    ---
    resoruce "aws_s3_bucket" "payroll_data" {
      bucket = "${var.app_region}-${var.bucket}"
    }
    # variables.tf
    ---
    variable "app_resion" {
      type = string
    }
    
    variable "ami" {
      type = string
    }
    
    variable "bucket" {
      default = "flexit-payroll-alpha-2024c"
    }
    
mkdir -p terraform-projects/modules/payroll-us
    # main.tf
    ---
    module "us_payroll" {
      source = "../modules/payroll-app"
      app_region = "us-east-1"
      ami = "ami-123456"
    }
    # provider.tf
    ---
    provider "aws" {
      region = "us-east-1"
    }

mkdir -p terraform-projects/modules/payroll-uk
    # main.tf
    ---
    module "uk_payroll" {
      source = "../modules/payroll-app"
      app_region = "us-west-2"
      ami = "ami-12345689"
    }
    # provider.tf
    ---
    provider "aws" {
      region = "us-west-2"
    }

Using modules from terrafrom registery
--------------------------------------
verified module  --> bluetick mark and validated by hashicorp
community module

module "security_group_ssh" {
  source "terrafrom-aws-modules/security-groups/aws/modules/ssh"
  version = "3.16.0'
  # insert the 2 required variables here
  vpc_id = "vpc_sdad"
  ingress_cidr_blocks = [ "10.10.0.0/16" ]
  name = "ssh-access"
}


