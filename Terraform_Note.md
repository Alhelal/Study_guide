Terraform Notes:
================

1.	In Terraform, data blocks are used to retrieve data from external sources, such as APIs or databases, and make that data available to your Terraform configuration. With data blocks, you can use information from external sources to drive your infrastructure as code, making it more dynamic and flexible.

	For example, you can use a data block to retrieve a list of Amazon Machine Images (AMIs) from AWS, and then use that data to select the appropriate AMI for a virtual machine you are provisioning:

		data "aws_ami" "example" {
		  most_recent = true
		 
		  filter {
		    name   = "name"
		    values = ["amzn2-ami-hvm-2.0.*-x86_64-gp2"]
		  }
		 
		  filter {
		    name   = "virtualization-type"
		    values = ["hvm"]
		  }
		}
		 
		resource "aws_instance" "example" {
		  ami           = data.aws_ami.example.id
		  instance_type = "t2.micro"
		}


2. 	Terraform by default provisions 10 resources concurrently during a `terraform apply` command to speed up the provisioning process and reduce the overall time taken.

3.	The terraform state command and its subcommands can be used for various tasks related to the Terraform state.

		Subcommands:
		    identities          List the identities of resources in the state
		    list                List resources in the state
		    mv                  Move an item in the state
		    pull                Pull current state and output to stdout
		    push                Update remote state from a local state file
		    replace-provider    Replace provider in the state
		    rm                  Remove instances from the state
		    show                Show a resource in the state

4.	Variable Reserved Names: Variable names cannot be any of the reserved names used by Terraform for specific constructs. 
	These reserved names include: 
			source, 
			version, 
			providers, 
			count, 
			for_each, 
			lifecycle, 
			depends_on,
			locals

5.	terraform workspace list 
	This command will display a list of workspaces, with an asterisk * indicating the currently selected workspace. 

	Select a different workspace.
	To switch to a different workspace, use the select command followed by the name of the desired workspace:

    terraform workspace select <workspace_name>

6.	The Terraform language uses the following types for its values: 
		string, 
		number, 
		bool, 
		list (or tuple), 
		map (or object). 

7.	The correct prefix string for setting input variables using environment variables in Terraform is TF_VAR. 
	This prefix is recognized by Terraform to assign values to variables.

8.	To destroy all Terraform-managed resources except for a single resource, you can use the terraform state command to remove the state for the resources you want to preserve. 
	This effectively tells Terraform that those resources no longer exist, so it will not attempt to destroy them when you run terraform destroy.

	Here's an example of how you could do this:

		# Identify the resource you want to preserve. In this example, let's assume you want to preserve a resource named prod_db.

		# Run terraform state list to see a list of all Terraform-managed resources.

		# Run terraform state rm for each resource you want to keep, like the prod_db.

		# Run terraform destroy to destroy all remaining resources.

9.	The list below contains all the requirements for publishing a module. Meeting the requirements for publishing a module is extremely easy. 
	The list may appear long only to ensure we're detailed, but adhering to the requirements should happen naturally.

		* GitHub. The module must be on GitHub and must be a public repo. This is only a requirement for the public registry. If you're using a private registry, you may ignore this requirement

		* Named terraform-<PROVIDER>-<NAME>. Module repositories must use this three-part name format, where <NAME> reflects the type of infrastructure the module manages and <PROVIDER> is the main provider where it creates that infrastructure. 
		The <NAME> segment can contain additional hyphens. Examples: terraform-google-vault or terraform-aws-ec2-instance.

		* Repository description. The GitHub repository description is used to populate the short description of the module. This should be a simple one-sentence description of the module.

		* Standard module structure. The module must adhere to the standard module structure. This allows the registry to inspect your module and generate documentation, track resource usage, parse submodules and examples, and more.

		* x.y.z tags for releases. The registry uses tags to identify module versions. Release tag names must be a semantic version, which can optionally be prefixed with a v. 
		For example, v1.0.4 and 0.9.2. To publish a module initially, at least one release tag must be present. Tags that don't look like version numbers are ignored.

10.  <code> terraform apply -replace </code>
    
        command manually marks a Terraform-managed resource for replacement, forcing it to be destroyed and recreated on the apply execution.
    
    ` terraform destroy -target (virtual machine) `
        
        You could also use terraform destroy -target (virtual machine) and destroy only the virtual machine and then run a terraform apply again.

11. To manage the resources created manually by Ron and Ginny in Terraform without negatively impacting the availability of the deployed resources, Harry can follow the steps below:

        1. Import the existing resources: Harry can use the terraform import command to import the existing resources into Terraform. The terraform import command allows you to import existing infrastructure into Terraform, creating a Terraform state file for the resources. You can also create an import block to pull in resources under Terraform management.

        2. Modify the Terraform configuration: After importing the resources, Harry can modify the Terraform configuration to reflect the desired state of the resources. This will allow him to manage the resources using Terraform just like any other Terraform-managed resource

        3. Test the changes: Before applying the changes, Harry can use the terraform plan command to preview the changes that will be made to the resources. This will allow him to verify that the changes will not negatively impact the availability of the resources.

        4. Apply the changes: If the changes are correct, Harry can use the terraform apply command to apply the changes to the resources.

12. The terraform login command can automatically obtain and save an API token for HCP Terraform.

13. Terraform can be expressed using two syntaxes: HashiCorp Configuration Language (HCL), which is the primary syntax for Terraform, and JSON.

14. HashiCorp, the creator of Terraform, recommends using two spaces for indentation when writing Terraform code. This is a convention that helps to improve readability and consistency across Terraform configurations.
        For example, when defining a resource in Terraform, you would use two spaces to indent each level of the resource definition, as in the following example:

        resource "aws_instance" "example" {
          ami           = "ami-0c55b159cbfafe1f0"
          instance_type = "t2.micro"

          tags = {
            Name = "example-instance"
          }
        }

15. By default, terraform init downloads plugins into a subdirectory of the working directory, .terraform/providers so that each working directory is self-contained.

16. The two Terraform commands used to download and update modules are:

        1. terraform init: This command downloads and updates the required modules for the Terraform configuration. It also sets up the backend for state storage if specified in the configuration.

        2. terraform get: This command is used to download and update modules for a Terraform configuration. It can be used to update specific modules by specifying the module name and version number, or it can be used to update all modules by simply running the command without any arguments.

17. Your team is developing a reusable Terraform module for web servers that must deploy exactly two instances, and you need to enforce this during the plan and apply phase.

        The validation block with condition = var.instance_count == 2 enforces that only the value 2 is accepted, and emits a clear error message otherwise. This leverages Terraform’s input variable validation for uniquely restrictive requirements.

        `variable "instance_count" {
          type = number

          validation {
            condition     = var.instance_count >= 2
            error_message = "You must request at least two web instances."
          }
        }`

18. The terraform force-unlock command can be used to remove the lock on the Terraform state for the current configuration. 
    Another option is to use the "terraform state rm" command followed by the "terraform state push" command to forcibly overwrite the state on the remote backend, effectively removing the lock. 
    It's important to note that these commands should be used with caution, as they can potentially cause conflicts and data loss if not used properly.

19. Terraform is written in HashiCorp Configuration Language (HCL). However, Terraform also supports a syntax that is JSON compatible

        1. Terraform is primarily designed on  **immutable**  infrastructure principles
        2. Terraform is also a ***declarative*** language, where you simply declare the desired state, and Terraform ensures that real-world resources match the desired state as written.

20. The private registry feature in HCP Terraform allows users to publish and maintain custom modules within their organization, providing a secure and controlled environment for sharing infrastructure configurations.

        You can use modules from a private registry, like the one provided by HCP Terraform. Private registry modules have source strings of the form <HOSTNAME>/<NAMESPACE>/<NAME>/<PROVIDER>. This is the same format as the public registry but with an added hostname prefix.

21. The required_version parameter in a terraform block is used to specify the minimum version of Terraform that is required to run the configuration. 
    This parameter is optional, but it can be useful for ensuring that a Terraform configuration is only run with a version of Terraform that is known to be compatible.

    For example, if your Terraform configuration uses features that were introduced in Terraform 1.9.2, you could include the following terraform block in your configuration to ensure that Terraform 1.9.2 or later is used:

        terraform {
          required_version = ">= 1.9.2"
        }

22. Terraform provides several backend options, including:

        1. local backend: The default backend, which stores Terraform state on the local filesystem. This backend is suitable for small, single-user deployments, but can become a bottleneck as the size of your infrastructure grows or as multiple users start managing the infrastructure.
        
        2. remote backend: This backend stores Terraform state in a remote location, such as an S3 bucket, a Consul server, or a Terraform Enterprise instance. The remote backend allows multiple users to share the same state and reduces the risk of state corruption due to disk failures or other issues.

        3. consul backend: This backend stores Terraform state in a Consul cluster. Consul provides a highly available and durable storage solution for Terraform state, and also provides features like locking and versioning that are important for collaboration.

        4. s3 backend: This backend stores Terraform state in an S3 bucket. S3 provides a highly available and durable storage solution for Terraform state, and is a popular option for storing Terraform state for large infrastructure deployments.

23. By default, Terraform does not provide the ability to mask secrets in the Terraform plan and state files regardless of what provider you are using. 
    While Terraform and Vault are both developed by HashiCorp and have a tight integration, masking secrets in Terraform plans and state files requires additional steps to securely manage sensitive information.

24. An alias meta-argument is used when using the same provider with different configurations for different resources. This feature allows you to include multiple provider blocks that refer to different configurations. 

    In this example, you would need something like this:

        provider "aws" {
          region  = "us-east-1"
        }

        provider "aws" {
          region = "us-west-1"
          alias  = "west"
        }

25. Terraform Community (Free) stores the local state for workspaces in a file on disk. For local state, Terraform stores the workspace states in a directory called terraform.tfstate.d/<workspace_name>. 

26. Terraform has detailed logs that can be enabled by setting the TF_LOG environment variable to any value. This will cause detailed logs to appear on stderr.

        You can set TF_LOG to one of the log levels TRACE, DEBUG, INFO, WARN or ERROR to change the verbosity of the logs. TRACE is the most verbose and it is the default if TF_LOG is set to something other than a log level name.

27. After approving a merge request that modifies Terraform configurations in a GitHub repository linked to an HCP Terraform workspace, the default action that can be expected to run automatically is a "speculative plan" operation.

        When you merge a pull request or push changes to the main branch (or any branch you have configured as the trigger for the workspace), HCP Terraform typically triggers a plan operation. During this plan phase, Terraform examines the proposed changes to your infrastructure and displays a list of actions it would take if applied. It's a way to preview the changes before actually making them.

        The plan output shows what resources Terraform would create, modify, or delete, which allows you to review and validate the expected changes. After reviewing the plan, you can then manually apply the changes to your infrastructure through the HCP Terraform workspace.

        Note: You can absolutely configure a Terraform workspace to automatically apply the changes to the code, although that is generally not recommended, nor is it the default action.

28. You found a module on the Terraform Registry that will provision the resources you need. What information can you find on the Terraform Registry to help you quickly use this module?

		Every page on the registry has a search field for finding modules. Enter any type of module you're looking for (examples: "vault," "vpc," "database"), and the resulting modules will be listed. The search query will look at the module name, provider, and description to match your search terms. On the results page, filters can be used to further refine search results.
		https://developer.hashicorp.com/terraform/registry/modules/use

29. In order to check the current version of Terraform you have installed, you can use the command

	terraform version

30.	Given the Terraform configuration below, which order will the resources be created?

			resource "aws_instance" "web_server" {
			    ami = "i-abdce12345"
			    instance_type = "t2.micro"
			}

			resource "aws_eip" "web_server_ip" { 
			    vpc = true 
			    instance = aws_instance.web_server.id 
			}

		aws_instance will be created first
		aws_eip will be created second

31.	When configuring a remote backend in Terraform, it might be a good idea to purposely omit some of the required arguments to ensure secrets and other relevant data are not inadvertently shared with others. What alternatives are 			available to provide the remaining values to Terraform to initialize and communicate with the remote backend?

		When configuring a remote backend in Terraform, it's important to avoid hardcoding sensitive data like secrets in your configuration files to prevent inadvertent sharing through version control. Instead, you can provide these values securely through alternative methods. One option is to provide the missing values interactively on the command line during terraform init, ensuring they aren't stored in code. Another approach is to use the -backend-config=PATH flag to specify a separate configuration file that can be excluded from Git repositories. Additionally, you can directly query HashiCorp Vault for secrets, allowing dynamic retrieval of credentials at runtime.

32.	True or False? State is a requirement for Terraform to function.

		True. 
		Without state, Terraform would not be able to plan, apply, or manage changes to your infrastructure.

33.	Which of the following describes the process of leveraging a local value within a Terraform module and exporting it for use by another module?

		Exporting the local value as an output from the first module, then importing it into the second module's configuration.

34.	In the example below, where is the value of the DNS record's IP address originating from?


		resource "aws_route53_record" "www" {
		  zone_id = aws_route53_zone.primary.zone_id
		  name    = "www.helloworld.com"
		  type    = "A"
		  ttl     = "300"
		  records = [module.web_server.instance_ip_addr]
		}

		the output of a module named web_server

35.	What happens when a terraform apply command is executed?

		applies the changes required in the target infrastructure in order to reach the desired configuration

36.	True or False? You can migrate the Terraform backend but only if there are no resources currently being managed.

		False
		You can migrate the Terraform backend even if there are resources currently being managed. Migrating the backend involves moving the state file and configuration to a new location, which can be done without impacting the resources being managed by Terraform.

37.	What is the downside to using Terraform to interact with sensitive data, such as reading secrets from Vault?

		secrets are persisted to the state file
		Using Terraform to interact with sensitive data like secrets from Vault can lead to the secrets being persisted to the state file. This poses a security risk as the state file may contain sensitive information that could be exposed if not properly managed or secured.

38.	Which of the following actions are performed during a terraform init?

		initializes the backend configuration
		downloads the required modules referenced in the configuration
		downloads the providers/plugins required to execute the configuration

39.	You want to start managing resources that were not originally provisioned through infrastructure as code. Before you can import the resource's current state, what must you do before running the terraform import command?

		update the Terraform configuration file to include the new resources that match the resources you want to import
		Before running the terraform import command, you need to update the Terraform configuration file to include the new resources that match the resources you want to import. This step ensures that Terraform is aware of the resources you want to manage and can properly import their current state.

40.	The `terraform apply -replace=` command is used to force a resource to be destroyed and recreated, even if there are no configuration changes that would require it. This is achieved by specifying the resource address that needs to be replaced, ensuring that the resource is recreated with the latest configuration.

41.	Stephen is writing brand new code and needs to ensure it is syntactically valid and internally consistent. Stephen doesn't want to wait for Terraform to access any remote services while making sure his code is valid. What command can he use to accomplish this?

		terraform validate
		The 'terraform validate' command is used to check whether the configuration files are syntactically valid and internally consistent without accessing any remote services. It is a quick way to ensure that the code is correctly written and structured before attempting to apply it.

42.	In order to make a Terraform configuration file dynamic and/or reusable, static values should be converted to use what?

		input variables
		Converting static values in a Terraform configuration file to use input variables allows for dynamic and reusable configurations. Input variables can be defined externally and passed into the Terraform configuration, making it easier to customize the behavior of the infrastructure without modifying the configuration file itself.

43.	Which of the following best describes a Terraform provider?

		a plugin that Terraform uses to translate the API interactions with the service or provider
		A Terraform provider is a plugin that Terraform uses to interact with a specific service or provider. It translates the API interactions between Terraform and the service, allowing Terraform to manage resources in that service.

44.	Which of the following Terraform files should be ignored by Git when committing code to a repo?

		terraform.tfvars
			The `terraform.tfvars` file contains variable values that are specific to a particular environment or deployment. It should be excluded from version control to prevent accidental exposure of sensitive information and to allow different environments to have their own variable values.

		terraform.tfstate
			The `terraform.tfstate` file stores the current state of the infrastructure managed by Terraform. It should not be committed to version control as it contains sensitive information and can be dynamically updated during Terraform operations.

45.	Aaron deployed multiple VMs outside the Terraform workflow, and now your team is unsure which VM is managed by Terraform. What approach would best help you identify the Terraform-managed VM without making any changes to the infrastructure?

		Use Terraform state commands (e.g., terraform state list and terraform state show) to match the tracked VM’s ID with the list of active VMs.
		Using Terraform state commands like terraform state list and terraform state show to match the tracked VM's ID with the list of active VMs is a valid method to identify the Terraform-managed VM. By comparing the state information stored by Terraform with the actual VM instances, you can determine which VMs are managed by Terraform.

46.	Please fill the blank field(s) in the statement by writing the appropriate word.

		terraform console

47.	When multiple engineers start deploying infrastructure using the same state file, what is a feature of remote state storage that is critical to ensure the state does not become corrupt?

		state locking
		State locking is a critical feature of remote state storage that prevents multiple engineers from deploying infrastructure using the same state file simultaneously. It ensures that only one engineer can make changes to the state at a time, preventing conflicts and potential corruption of the state file.

48.	HCP Terraform can be managed from the CLI but requires an API token

49.	When using variables in HCP Terraform, what level of scope can the variable be applied to?

		HCP Terraform allows you to store important values in one place, which you can use across multiple projects. You can easily update the values, and the changes will apply to all projects that use them. Additionally, you can modify the values for specific projects without affecting others that use the same values. HCP Terraform allows you to use variables within a workspace, or use variable sets that can be used across multiple (or all) HCP Terraform workspaces.

		Run-specific variables can be used by setting Terraform variable values using the -var and -var-file arguments in a single workspace

		You can create a variable set by adding variables to the variable set and then applying a variable set scope so it can be used by multiple HCP Terraform workspaces

		You can also apply the variable set globally, which will apply the variable set to all existing and future workspaces

50.	Terraform language has built-in syntax for creating lists. Which of the following is the correct syntax to create a list in Terraform?

		["string1", "string2", "string3"]
		The square brackets [ ] are the correct syntax for creating lists in Terraform.

51.	Rick is writing a new Terraform configuration file and wishes to use modules in order to easily consume Terraform code that has already been written. Which of the modules shown below will be created first?

		terraform {
		  required_providers {
		    aws = {
		      source = "hashicorp/aws"
		    }
		  }
		}

		provider "aws" {
		  region = "us-west-2"
		}

		module "vpc" {
		  source  = "terraform-aws-modules/vpc/aws"
		  version = "2.21.0"

		  name = var.vpc_name
		  cidr = var.vpc_cidr

		  azs             = var.vpc_azs
		  private_subnets = var.vpc_private_subnets
		  public_subnets  = var.vpc_public_subnets

		  enable_nat_gateway = var.vpc_enable_nat_gateway

		  tags = var.vpc_tags
		}

		module "ec2_instances" {
		  source  = "terraform-aws-modules/ec2-instance/aws"
		  version = "2.12.0"

		  name           = "my-ec2-cluster"
		  instance_count = 2

		  ami                    = "ami-0c5204531f799e0c6"
		  instance_type          = "t2.micro"
		  vpc_security_group_ids = [module.vpc.default_security_group_id]
		  subnet_id              = module.vpc.public_subnets[0]

		  tags = {
		    Terraform   = "true"
		    Environment = "dev"
		  }
		}


		module "vpc"
		The module "vpc" will be created first because Terraform processes modules in the order they are defined in the configuration file. Since the "vpc" module is defined before the "ec2_instances" module in the file, it will be created and initialized first before moving on to the "ec2_instances" module.

52.	True or False? Similar to Terraform Community, you must use the CLI to switch between workspaces when using HCP Terraform workspaces.

		False. 
		In HCP Terraform, you can switch between workspaces directly within the HCP Terraform web interface without the need to use the CLI. HCP Terraform provides a user-friendly interface that allows users to manage and switch between workspaces efficiently without relying solely on the CLI for workspace management.

53.	What is the best and easiest way for Terraform to read and write secrets from HashiCorp Vault?

		Vault provider
		The Vault provider in Terraform allows for seamless integration with HashiCorp Vault, enabling Terraform to securely read and write secrets directly from Vault. This is the recommended and easiest way to manage secrets within Terraform configurations.

54.	Workspaces provide similar functionality in the Community and HCP Terraform versions of Terraform.

		False. 
		While workspaces in the Community and HCP Terraform versions of Terraform serve the same purpose of managing multiple environments and configurations, there are differences in how they are implemented and accessed. HCP Terraform offers additional features and capabilities for managing workspaces, such as remote state management, collaboration tools, and version control integration, which are not available in the Community version.

55.	What Terraform command can be used to inspect the current state file?

		terraform show
		The 'terraform show' command is used to inspect the current state file in Terraform. It displays the current state as Terraform sees it, including resource attributes and dependencies.

56.	Using a dynamic block in Terraform allows you to iterate over a list of items, such as a list of required tcp ports, and dynamically create resources based on those items. This feature helps reduce the amount of code needed to define multiple similar resources, making it the ideal choice for adding multiple tcp ports to the new security group with minimal lines of code.

57.	Which of the following allows Terraform users to apply policy as code to enforce standardized configurations for resources being deployed via infrastructure as code?

		Sentinel is the correct choice as it is a policy as code framework that allows users to define and enforce policies for Terraform configurations. It enables users to apply standardized configurations, validate resource attributes, and ensure compliance with organizational policies before deploying infrastructure.

58.	Terry is using a module to deploy some EC2 instances on AWS for a new project. He is viewing the code that is calling the module for deployment, which is shown below. Where does the value of the security group originate?

		module "ec2_instances" {
		  source  = "terraform-aws-modules/ec2-instance/aws"
		  version = "4.3.0"

		  name           = "my-ec2-cluster"
		  instance_count = 2

		  ami                    = "ami-0c5204531f799e0c6"
		  instance_type          = "t2.micro"
		  vpc_security_group_ids = [module.vpc.default_security_group_id]
		  subnet_id              = module.vpc.public_subnets[0]

		  tag = {
		    Terraform   = "true"
		    Environment = "dev"
		  }


		The value of the security group originates from the output of another module, specifically the default_security_group_id output of the vpc module. This allows for the reuse of existing resources and promotes modularity in Terraform code.

59.	The terraform fmt command in Terraform is used to automatically format Terraform configuration files according to a consistent style defined by the Terraform language. This command helps ensure that your Terraform code follows a standard formatting convention, making it easier to read and maintain.

60.	After executing a terraform plan, you notice that a resource has a tilde (~) next to it. What does this mean?

		When a resource has a tilde (~) next to it in the terraform plan, it signifies that the resource will be updated in place, preserving its current state and making necessary modifications without recreating it.

61.	Which of the following is considered a Terraform plugin?

		A Terraform provider is a type of plugin that extends Terraform's functionality by enabling it to interact with specific types of infrastructure APIs.

62.	What happens when a terraform plan is executed?

		creates an execution plan and determines what changes are required to achieve the desired state in the configuration files.

63.	Frank has a file named main.tf which is shown below. Which of the following statements are true about this code? (select two)


		module "servers" {
		  source = "./modules/app-cluster"

		  servers = 5
		}


		main.tf is the calling module
		app-cluster is the child module

64.	In HCP Terraform, a workspace can be mapped to how many VCS repos?

		One.
		In HCP Terraform, a workspace can be mapped to only one VCS (Version Control System) repository. This means that the workspace will be associated with a single repository where the Terraform configuration files are stored and managed.

65.	By default, where does Terraform Community/CLI store its state file?

		current working directory

66.	Kristen is using modules to provision an Azure environment for a new application. She is using the following code to specify the version of her virtual machine module. Which of the following Terraform features supports the versioning of a module? (select two)

		module "compute" {
		  source  = "azure/compute/azurerm"
		  version = "5.1.0"

		  resource_group_name = "production_web"
		  vnet_subnet_id      = azurerm_subnet.aks-default.id 
		}

		private registry
		Private registries allow organizations to host and manage their own modules internally. By using a private registry, Kristen can version her modules internally and ensure that her infrastructure deployments are consistent and controlled within her organization.

67.	Multiple providers can be declared within a single Terraform configuration file.

		True.
		Multiple providers can indeed be declared within a single Terraform configuration file. This allows users to manage resources from different cloud providers or services within the same Terraform configuration, making it easier to maintain and manage infrastructure across various platforms.

68.	By default, a child module will have access to all variables set in the calling (parent) module.

		False.
		by default, a child module in Terraform does not inherit all variables set in the calling (parent) module. The parent module must explicitly pass variables to the child module by defining input variables in the child module's configuration.

69.	What Terraform feature is shown in the example below?

			 resource "aws_security_group" "example" {
			  name = "sg-app-web-01"

			  dynamic "ingress" {
			    for_each = var.service_ports
			    content {
			      from_port = ingress.value
			      to_port   = ingress.value
			      protocol  = "tcp"
			    }
			  }
			}

		dynamic block

70.	In the terraform block, which configuration would be used to identify the specific version of a provider required?

		required_providers

71.	When using HCP Terraform, what is the easiest way to ensure the security and integrity of modules when used by multiple teams across different projects?

		Use the HCP Terraform Private Registry to ensure only approved modules are consumed by your organization
		Using the HCP Terraform Private Registry allows organizations to control and manage the modules that are available for consumption by their teams. This ensures that only approved and verified modules are used in different projects, enhancing security and integrity across the organization.

72.	Which feature of HCP Terraform can be used to enforce fine-grained policies to enforce standardization and cost controls before resources are provisioned with Terraform?

		sentinel and OPA
		Sentinel and OPA are both policy as code tools that can be integrated with HCP Terraform to enforce fine-grained policies. These tools allow organizations to define and enforce policies that govern infrastructure provisioning, ensuring standardization and cost controls are in place before resources are provisioned with Terraform.

73.	Your organization requires that no security group in your public cloud environment includes "0.0.0.0/0" as a source of network traffic. How can you proactively enforce this control and prevent Terraform configurations from being executed if they contain this specific string?

		Create a Sentinel or OPA policy that checks for the string and denies the Terraform apply if the string exists
		Creating a Sentinel or OPA policy that specifically checks for the presence of "0.0.0.0/0" in security group configurations allows for proactive enforcement of the control. By denying the Terraform apply if the string exists, you can prevent any configurations that violate the organization's security requirements from being executed.

74.	Based on the following code, which code block will create a resource first?

			resource "aws_instance" "data_processing" {
			  ami           = data.aws_ami.amazon_linux.id
			  instance_type = "t2.micro"

			  depends_on = [aws_s3_bucket.customer_data]
			}

			module "example_sqs_queue" {
			  source  = "terraform-aws-modules/sqs/aws"
			  version = "2.1.0"

			  depends_on = [aws_s3_bucket.customer_data, aws_instance.data_processing]
			}

			resource "aws_s3_bucket" "customer_data" {
			  acl = "private"
			}

			resource "aws_eip" "ip" {
			  vpc      = true
			  instance = aws_instance.data_processing.id
			}

		aws_s3_bucket.customer_data

75.	A main.tf file is always required when using Terraform?

		False. 
		While a main.tf file is often used to contain the primary configuration in Terraform projects, it is not always necessary. Terraform allows for modular and flexible file structures, so the configuration can be spread across multiple files or organized in a different way based on the project's needs. As long as the Terraform code can locate and interpret the configuration files, the presence of a main.tf file is not mandatory.

76.	HCP Terraform provides organizations with many features not available to those running Terraform Community to deploy infrastructure. Select the ADDITIONAL features that organizations can take advantage of by moving to HCP Terraform.

		remote runs
		VCS connection
		private registry

77.	The command __ is used to extract the output values defined in the Terraform configuration.

		terraform output

78.	Rigby is implementing Terraform and was given a configuration that includes the snippet below. Where is this particular module stored?

			module "consul" {
			  source  = "hashicorp/consul/aws"
			  version = "0.1.0"
			}

		public Terraform registry
		The source "hashicorp/consul/aws" in the module configuration indicates that the module is stored in the public Terraform registry. This means that the module can be accessed and downloaded by anyone using Terraform, making it a convenient and widely available option for sharing and reusing modules.

79.	Jeff is a DevOps Engineer for a large company and is currently managing the infrastructure for many different applications using Terraform. Recently, Jeff received a request to remove a specific VMware virtual machine from Terraform as the application team no longer needs it. Jeff opens his terminal and issues the command:

			$ terraform state rm vsphere_virtual_machine.app1

			Removed vsphere_virtual_machine.app1
			Successfully removed 1 resource instance(s).

		The next time that Jeff runs a terraform apply, the resource is not marked to be deleted. In fact, Terraform is stating that it is creating another identical resource.

		.....
		An execution plan has been generated and is shown below.  
		Resource actions are indicated with the following symbols:
		  + create

		Terraform will perform the following actions:

		  # vsphere_virtual_machine.app1 will be created
	What would explain this behavior?

	Jeff removed the resource from the state file, and not the configuration file. Therefore, Terraform is no longer aware of the virtual machine and assumes Jeff wants to create a new one since the virtual machine is still in the Terraform configuration file

80.	Terraform is designed to work only with public cloud platforms, and organizations that wish to use it for on-premises infrastructure (private cloud) should look for an alternative solution.

		False
		Terraform is not exclusively designed for public cloud platforms. It is a flexible tool that can be used to manage infrastructure across different environments, including on-premises infrastructure (private cloud). 

81.	Using the Terraform code below, where will the resource be provisioned?

			provider "aws" {
			  region = "us-east-1"
			}

			provider "aws" {
			  alias  = "west"
			  region = "us-west-2"
			}

			provider "aws" {
			  alias  = "eu"
			  region = "eu-west-2"
			}

			resource "aws_instance" "vault" {
			  ami                    = data.aws_ami.amzlinux2.id
			  instance_type          = "t3.micro"
			  key_name               = "krausen_key"
			  vpc_security_group_ids = var.vault_sg
			  subnet_id              = var.vault_subnet
			  user_data              = file("vault.sh")

			  tags = {
			    Name = "vault"
			  }
			}

		us-east-1
		The resource "aws_instance" named "vault" will be provisioned in the "us-east-1" region because the default provider configuration specifies this region. Since no alias is provided for this provider, it is the default provider for the resource.

82.	Based on the Terraform code below, what block type is used to define the VPC?

			vpc_id = aws_vpc.main.id
			...

		resource block

83.	What function does the terraform init -upgrade command perform?

		update all previously installed plugins and modules to the newest version that complies with the configuration’s version constraints

84.	Ralphie has executed a terraform apply using a complex Terraform configuration file. However, a few resources failed to deploy due to incorrect variables. After the error is discovered, what happens to the resources that were successfully provisioned?

		the resources that were successfully provisioned will remain as deployed

85.	Given the following snippet of code, what will the value of the "Name" tag equal after a terraform apply?

			variable "name" {
			  description = "The username assigned to the infrastructure"
			  default = "data_processing"
			}

			variable "team" {
			  description = "The team responsible for the infrastructure"
			  default = "IS Team"
			}

			locals {
			  name  = (var.name != "" ? var.name : random_id.id.hex)
			  owner = var.team
			  common_tags = {
			    Owner = local.owner
			    Name  = local.name
			  }
			}

		data_processing
		The "Name" tag is derived from the local variable "name," which is determined by the value of the "name" variable provided in the Terraform configuration. Since the default value of the "name" variable is "data_processing," the "Name" tag will equal "data_processing." 

86.	All of your infrastructure is managed by Terraform. Despite requests to avoid changes outside Terraform, engineers sometimes make minor updates directly in the cloud platform. What Terraform command can reconcile the state with the actual infrastructure to detect any drift from the last-known state?

		terraform apply -refresh-only
		The "terraform apply -refresh-only" command is used to reconcile the state with the real-world infrastructure by refreshing the state without making any changes. It detects any drift from the last-known state by comparing the current state with the actual infrastructure, highlighting any differences that need to be addressed.

87.	Given the following snippet of code, what does servers = 4 reference?

			module "servers" {
			  source = "./modules/aws-servers"

			  servers = 4
			}

		the value of an input variable
		In Terraform, the `servers = 4` line in the code snippet refers to the value of an input variable named `servers`. This input variable is likely defined within the module "aws-servers" and is being set to a value of 4 in this particular instantiation of the module.

88.	Teddy is using Terraform to deploy infrastructure using modules. Where is the module below stored?

			module "monitoring_tools" {
			  source = "./modules/monitoring_tools"

			  cluster_hostname = module.k8s_cluster.hostname
			}

		locally on the instance running Terraform

89.	Based on the code provided, how many subnets will be created in the AWS account?

		variables.tf

			variable "private_subnet_names" {
			  type    = list(string)
			  default = ["private_subnet_a", "private_subnet_b", "private_subnet_c"]
			}
			variable "vpc_cidr" {
			  type    = string
			  default = "10.0.0.0/16"
			}
			variable "public_subnet_names" {
			  type    = list(string)
			  default = ["public_subnet_1", "public_subnet_2"]
			}
		main.tf

			resource "aws_subnet" "private_subnet" {
			  count             = length(var.private_subnet_names)
			  vpc_id            = aws_vpc.vpc.id
			  cidr_block        = cidrsubnet(var.vpc_cidr, 8, count.index)
			  availability_zone = data.aws_availability_zones.available.names[count.index]

			  tags = {
			    Name      = var.private_subnet_names[count.index]
			    Terraform = "true"
			  }
			}

		3.
		The code snippet creates subnets based on the number of elements in the "private_subnet_names" list, which has 3 elements. Therefore, 3 subnets will be created in the AWS account, each with a unique name and CIDR block within the VPC range.

90.	When a terraform apply is executed, where is the AWS provider retrieving credentials to create cloud resources in the code snippet below?

			provider "aws" {
			   region     = us-east-1
			   access_key = data.vault_aws_access_credentials.creds.access_key
			   secret_key = data.vault_aws_access_credentials.creds.secret_key
			}

		from a data source that is retrieving credentials from HashiCorp Vault. Vault is dynamically generating the credentials on Terraform's behalf.
		The code snippet is using a data source to retrieve AWS credentials from HashiCorp Vault. Vault dynamically generates these credentials on behalf of Terraform, ensuring secure and dynamic access to AWS resources without exposing sensitive information in the code.

91.	Which block type is used to assign a name to an expression that can be used multiple times within a module without having to repeat it?

		locals {}
		The locals {} block type is used to define local variables within a Terraform module. By assigning a name to an expression using locals {}, you can reuse that expression multiple times within the module without having to repeat it, making your code more concise and maintainable.

92.	When running the terraform validate command, which issue will be brought to your attention?




93.	Terraform has detailed logs that can be enabled using the TF_LOG environment variable. Which of the following log levels is the most verbose, meaning it will log the most specific logs?

		TRACE
		The TRACE log level in Terraform is the most verbose level, providing the most detailed and specific logs. It logs every action taken by Terraform, including individual resource creation, updates, and deletions, making it ideal for troubleshooting and debugging complex issues.

94.	What is the primary function of HCP Terraform agents?

		execute Terraform plans and apply changes to infrastructure
		HCP Terraform agents are primarily responsible for executing Terraform plans and applying changes to infrastructure. They act as the bridge between the HCP Terraform service and the target infrastructure, ensuring that the desired state of the infrastructure is achieved based on the Terraform configuration.

95.	Michael has deployed many resources in AWS using Terraform and can easily update or destroy resources when required by the application team. A new employee, Dwight, is working with the application team and deployed a new EC2 instance through the AWS console. When Michael finds out, he decided he wants to manage the new EC2 instance using Terraform moving forward. He opens his terminal and types:

	$ terraform import aws_instance.web_app_42 i-b54a26b28b8acv7233

	However, Terraform returns the following error: Error: resource address "aws_instance.web_app_42" does not exist in the configuration.

	What does Michael need to do first in order to manage the new Amazon EC2 instance with Terraform?

		create a configuration for the new resource in the Terraform configuration file, such as:

			resource "aws_instance" "web_app_42" {
			  # (resource arguments)
			}

96.	You are working with a cloud provider to deploy resources using Terraform. You've added the following data block to your configuration. When the data block is used, what data will be returned?

		data "aws_ami" "amzlinux2" {
		  most_recent = true
		  owners      = ["amazon"]

		  filter {
		    name   = "name"
		    values = ["amzn2-ami-hvm-*-x86_64-ebs"]
		  }
		}

		resource "aws_instance" "vault" {
		  ami                         = data.aws_ami.amzlinux2.id
		  instance_type               = "t3.micro"
		  key_name                    = "vault-key"
		  vpc_security_group_ids      = var.sg
		  subnet_id                   = var.subnet
		  associate_public_ip_address = "true"
		  user_data                   = file("vault.sh")

		  tags = {
		    Name = "vault"
		  }
		}

		all possible data of a specific Amazon Machine Image(AMI) from AWS
		The data block "aws_ami" with the specified filters will return all possible data of a specific Amazon Machine Image (AMI) from AWS that matches the criteria set in the configuration. This includes information such as the AMI ID, description, architecture, root device type, and more.

97.	What feature of Terraform provides an abstraction above the upstream API and is responsible for understanding API interactions and exposing resources?

		Terraform provider
		Terraform provider is the correct choice because it is a key component of Terraform that acts as an interface between Terraform and the upstream API of a specific service or platform.

98.	You are deploying a new application and want to deploy it to multiple AWS regions within the same configuration file. Which of the following features will allow you to configure this?

		multiple provider blocks using an alias

99.	Infrastructure as Code (IaC) provides many benefits to help organizations deploy application infrastructure much faster than clicking around in the console. What are the additional benefits of IaC?

		creates a blueprint of your data center
		allows infrastructure to be versioned
		code can easily be shared and reused

100. A startup needs a way to ensure only its engineers and architects can create and publish approved Terraform modules. Which feature can provide this capability?

		Private Registry
		Private Registry allows organizations to host their own Terraform modules internally, ensuring that only authorized engineers and architects within the organization can access and use them. This feature provides the necessary security and control over proprietary infrastructure configurations.

101. Any sensitive values referenced in the Terraform code, even as variables, will end up in plain text in the state file.

		True. 
		Any sensitive values referenced in the Terraform code, including variables, will be stored in plain text in the state file. This poses a security risk as anyone with access to the state file can potentially view these sensitive values, compromising the security of the infrastructure.

102. Variables and their default values are typically declared in a main.tf or variables.tf file. What type of file can be used to set explicit values for the current working directory that will override the default variable values?

		.tfvars file
		The .tfvars file is used to set explicit values for variables in Terraform. By creating a .tfvars file in the current working directory, you can override the default variable values declared in main.tf or variables.tf. This allows for flexibility in providing specific values for variables without modifying the main configuration files.

103. Which of the following best describes a "data source"?

		enables Terraform to fetch data for use elsewhere in the Terraform configuration
		A data source in Terraform enables the configuration to fetch data from an external source, such as an API or a cloud provider, to be used elsewhere in the Terraform configuration. This allows Terraform to dynamically retrieve information needed for resource provisioning.

104. You have created a new workspace for a project and added all of your Terraform configuration files in the new directory. Before you execute a terraform plan, you want to validate the configuration using the terraform validate command. However, Terraform returns the error:

			$ terraform validate
			Error: Could not load plugin

		the directory was not initialized

105. Given a Terraform config that includes the following code, how would you reference the instance associated with the last key in the map as written?

			resource "aws_instance" "database" {
			  # ...
			  for_each = {
			    "vault":     "secrets",
			    "terraform": "infrastructure",
			    "consul":    "networking",
			    "nomad":     "scheduler"
			  }
			}

		aws_instance.database["nomad"]

106. A terraform plan is a required step before running a terraform apply?

		False.
		While it is highly recommended to run a terraform plan to preview changes and ensure accuracy, it is not a mandatory step before running a terraform apply. You can directly apply your Terraform configuration without running a plan, but it is considered a best practice to do so to avoid unexpected outcomes.

107. You have deployed your production environment with Terraform, and a developer has requested that you update a load balancer configuration to improve its compatibility with their application. Once you have modified your Terraform configuration, how can you conduct a dry run to verify that no unexpected changes will be made?

		run  terraform plan and inspect the proposed changes

108. To force the destruction of resources without being prompted for confirmation, you can use the command

		terraform destroy -auto-approve

109. What CLI command and flag can you use to delete a resource named azurerm_resource_group.production that is managed by Terraform?

		terraform destroy -target=azurerm_resource_group.production

110. What CLI commands will completely tear down and delete all resources that Terraform is currently managing? (select two)

		terraform destroy
		terraform apply -destroy 
			The `terraform apply -destroy` command is another way to completely tear down and delete all resources that Terraform is currently managing.


111. You have an existing resource in your public cloud that was deployed manually, but you want the ability to reference different attributes of that resource throughout your configuration without hardcoding any values. How can you accomplish this?

		Add a data block to your configuration to query the existing resource. Use the available exported attributes of that resource type as needed throughout your configuration to get the values you need.

112. terraform validate will validate the syntax of your HCL files.

		True. 
		The `terraform validate` command is used to validate the syntax of your HashiCorp Configuration Language (HCL) files. It checks for any syntax errors or incorrect configurations in your Terraform code before attempting to apply or plan changes.

113. You have recently cloned a repo containing Terraform that you want to test in your environment. Once you customize the configuration, you run a terraform apply but it immediately fails. Why would the apply fail?

		Terraform needs to initialize the directory and download the required plugins

114. You need to use Terraform to destroy and recreate a single database server that was deployed alongside other resources. You don't want to modify the Terraform code. What command can accomplish this task?

		terraform apply -replace="aws_instance.database"
		The correct command to destroy and recreate a single resource without modifying the Terraform code is terraform apply -replace="aws_instance.database". This command will replace the specified resource with a new one, effectively destroying and recreating it within the infrastructure.

115. You have a module named prod_subnet that outputs the subnet_id of the subnet created by the module. How would you reference the subnet ID when using it for an input of another module? 

		subnet = module.prod_subnet.subnet_id

116. You need to use multiple resources from different providers in Terraform to accomplish a task. Which of the following can be used to configure the settings for each of the providers?


			provider "consul" {
			  address = "https://consul.krausen.com:8500"  
			  namespace = "developer"
			  token = "45a3bd52-07c7-47a4-52fd-0745e0cfe967"
			}

			provider "vault" {
			  address = "https://vault.krausen.com:8200"
			  namespace = "developer"
			}
		This choice correctly configures the settings for each provider by using the provider block for each provider separately. It specifies the address, namespace, and token for the "consul" provider and the address and namespace for the "vault" provider, allowing you to define the necessary configurations for each provider individually.

117. After using Terraform locally to deploy cloud resources, you have decided to move your state file to an Amazon S3 remote backend. You configure Terraform with the proper configuration as shown below. What command should be run in order to complete the state migration while copying the existing state to the new backend?

		terraform {
		  backend "s3" {
		    bucket = "tf-bucket"
		    key = "terraform/krausen/"
		    region = "us-east-1"
		  }
		}

		terraform init -migrate-state

118. When using HCP Terraform, creating a pull request on the branch of the workspace's linked repository will automatically trigger a speculative plan on a workspace.

		True. 
		When using HCP Terraform, creating a pull request on the branch of the workspace's linked repository will indeed automatically trigger a speculative plan on the workspace. This allows for changes to be previewed and evaluated before merging them into the main branch, ensuring smooth and controlled deployment processes.

119. Steve is a developer who is deploying resources to AWS using Terraform. Steve needs to gather detailed information about an EC2 instance named "frontend"that he deployed earlier in the day. What command can Steve use to view this detailed information?

		terraform state show aws_instance.frontend

120. You have deployed your network architecture in AWS using Terraform. A colleague recently logged in to the AWS console and made a change manually and now you need to be sure your Terraform state reflects the new change.
	What command should you run to update your Terraform state?

		terraform apply -refresh-only

121. Which of the following code snippets will properly configure a Terraform backend?

		terraform {
		  backend "remote" {
		    hostname = "app.terraform.io"
		    organization = "btk"

		  workspaces {
		    name = "bryan-prod"
		  }
		 }
		}

122. 
