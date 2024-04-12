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
      Idempotent --> This means you can run code multiple times, it will only make changes that are necessary to bring the environment into a defined state. It would leave anything already in place as it is, without us having to write any additional code.

**2. Server Temaplating Tools**

  - Docker
  - Packer
  - Hashocorp Vagrant

      The most common examples for server templated images are VM images such as those that are offered on osboxes.org, custom AMIs in Amazon AWS, and Docker images on DockerHub and other container registries. 
      Server templating tools also promote immutable infrastructure unlike configuration management tools. This means that, once the VM or a container is deployed, it is designed to remain unchanged.
      If there are changes to be made to the image, instead of updating the running instance, like in the case of configuration management tools such as Ansible,we update the image and then re-deploy a new instance using the updated image.

**3. Provisioning Tool**

  - Hashicorp Terraform
  - Cloudformation

      Deploy Immutable Infrastructure Resoruces - These tools are used to provision infrastructure components using a simple declarative code.
      These infrastructure components can range from servers such virtual machines, databases, VPCs, subnets,security groups, storage, and just about any services based on the provider we choose.
      Supports multiple providers.
