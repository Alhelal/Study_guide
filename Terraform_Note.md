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