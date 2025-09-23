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