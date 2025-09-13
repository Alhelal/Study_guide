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
    
    <code>terraform destroy -target (virtual machine)</code>
        
        You could also use terraform destroy -target (virtual machine) and destroy only the virtual machine and then run a terraform apply again.