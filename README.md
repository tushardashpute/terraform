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



















