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

        variable "instance_count" {
          type = number

          validation {
            condition     = var.instance_count >= 2
            error_message = "You must request at least two web instances."
          }
        }

18. 
