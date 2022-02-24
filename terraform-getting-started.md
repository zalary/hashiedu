# Getting Started with Terraform

Terraform is the most popular langauge for defining and provisioning infrastructure as code (IaC).

IaC tools allow you to manage infrastructure with configuration files rather than through a graphical user interface. IaC allows you to build, change, and manage your infrastructure in a safe, consistent, and repeatable way by defining resource configurations that you can version, reuse, and share.

## Prerequistes
- [Docker Desktop](https://docs.docker.com/desktop/#download-and-install) or [Docker Engine](https://docs.docker.com/engine/install/) for your environment

## Install Terraform
To install Terraform, simply visit [Terraform.io](https://www.terraform.io/downloads.html), download and unzip the appropriate binary package or install via a package manager.  We have a more detailed [installation instructions](https://learn.hashicorp.com/tutorials/terraform/install-cli) in our [Terraform Learn Guide](https://learn.hashicorp.com/terraform).

Once installed, you may need to add terraform to your path for [Unix/Linux](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux-unix) or [Windows](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows).

## Verify the Installation
You can verify that Terraform is installed correctly by using `terraform -help` to list Terraform's available subcommands. You can also add any subcommand to `terraform -help` to learn more about what that command does and see any available options.

```shell
terraform -help  
Usage: terraform [global options] <subcommand> [args]

The available commands for execution are listed below.
The primary workflow commands are given first, followed by
less common or more advanced commands.

Main commands:
  init          Prepare your working directory for other commands
  validate      Check whether the configuration is valid
  plan          Show changes required by the current configuration
  apply         Create or update infrastructure
  destroy       Destroy previously-created infrastructure

All other commands:
  console       Try Terraform expressions at an interactive command prompt
  fmt           Reformat your configuration in the standard style
  force-unlock  Release a stuck lock on the current workspace
  get           Install or upgrade remote Terraform modules
  graph         Generate a Graphviz graph of the steps in an operation
  import        Associate existing infrastructure with a Terraform resource
  login         Obtain and save credentials for a remote host
  logout        Remove locally-stored credentials for a remote host
  output        Show output values from your root module
  providers     Show the providers required for this configuration
  refresh       Update the state to match remote systems
  show          Show the current state or a saved plan
  state         Advanced state management
  taint         Mark a resource instance as not fully functional
  test          Experimental support for module integration testing
  untaint       Remove the 'tainted' state from a resource instance
  version       Show the current Terraform version
  workspace     Workspace management

Global options (use these before the subcommand, if any):
  -chdir=DIR    Switch to a different working directory before executing the
                given subcommand.
  -help         Show this help output, or the help for a specified subcommand.
  -version      An alias for the "version" subcommand.
```

With Terraform installed, let's dive right into it and start creating some infrastructure.

Most guys find it easiest to create a new directory on there local machine and create Terraform configuration code inside it.

```shell
$ mkdir terraform-demo
$ cd terraform-demo
```

Next, create a file for your Terraform configuration code.

```shell
$ touch main.tf
```

Paste the following lines into the file.

```hcl
terraform {
  required_providers {
    docker = {
      source = "kreuzwerker/docker"
    }
  }
}
provider "docker" {
    host = "unix:///var/run/docker.sock"
}
resource "docker_container" "nginx" {
  image = docker_image.nginx.latest
  name  = "training"
  ports {
    internal = 80
    external = 80
  }
}
resource "docker_image" "nginx" {
  name = "nginx:latest"
}
```

Initialize Terraform with the `init` command. The AWS provider will be installed. 

```shell
$ terraform init
```

You shoud check for any errors. If it ran successfully, provision the resource with the `apply` command.

```shell
$ terraform apply
```

The command will take up to a few minutes to run and will display a message indicating that the resource was created.

Finally, destroy the infrastructure.

```shell
$ terraform destroy
```

Look for a message are the bottom of the output asking for confirmation. Type `yes` and hit ENTER. Terraform will destroy the resources it had created earlier.
