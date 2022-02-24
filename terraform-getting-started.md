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

## Write configuration

The set of files used to describe infrastructure in Terraform is known as a Terraform configuration. You will write your first configuration to define a single Docker instance.

Each Terraform configuration must be in its own working directory. Create a directory for your configuration.

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

## Initialize Terraform
Initializing a configuration directory downloads and installs the providers defined in the configuration. In this case we are configuring the `docker` provider which will run an NGINX server.

Initialize Terraform with the `init` command.

```shell
$ terraform init
```


A successful initialization should look similar to the below:

```shell
Initializing the backend...

Initializing provider plugins...
- Finding latest version of kreuzwerker/docker...
- Installing kreuzwerker/docker v2.16.0...
- Installed kreuzwerker/docker v2.16.0 (self-signed, key ID BD080C4571C6104C)

Partner and community providers are signed by their developers.
If you'd like to know more about provider signing, you can read about it here:
https://www.terraform.io/docs/cli/plugins/signing.html

Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

## Create Infrastructure
Once you've successfully initialized Terraform, provision the resource with the `apply` command.

```shell
$ terraform apply
```

The command may take a few minutes to run, and will ask for confirmation before performing the apply.  Type `yes` at the prompt, then hit ENTER to proceed.

```shell
Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # docker_container.nginx will be created
  + resource "docker_container" "nginx" {
      + attach           = false
      + bridge           = (known after apply)
      + command          = (known after apply)
      + container_logs   = (known after apply)
      + entrypoint       = (known after apply)
      + env              = (known after apply)
      + exit_code        = (known after apply)
      + gateway          = (known after apply)
      + hostname         = (known after apply)
      + id               = (known after apply)
      + image            = (known after apply)
      + init             = (known after apply)
      + ip_address       = (known after apply)
      + ip_prefix_length = (known after apply)
      + ipc_mode         = (known after apply)
      + log_driver       = (known after apply)
      + logs             = false
      + must_run         = true
      + name             = "training"
      + network_data     = (known after apply)
      + read_only        = false
      + remove_volumes   = true
      + restart          = "no"
      + rm               = false
      + security_opts    = (known after apply)
      + shm_size         = (known after apply)
      + start            = true
      + stdin_open       = false
      + tty              = false

      + healthcheck {
          + interval     = (known after apply)
          + retries      = (known after apply)
          + start_period = (known after apply)
          + test         = (known after apply)
          + timeout      = (known after apply)
        }

      + labels {
          + label = (known after apply)
          + value = (known after apply)
        }

      + ports {
          + external = 80
          + internal = 80
          + ip       = "0.0.0.0"
          + protocol = "tcp"
        }
    }

  # docker_image.nginx will be created
  + resource "docker_image" "nginx" {
      + id          = (known after apply)
      + latest      = (known after apply)
      + name        = "nginx:latest"
      + output      = (known after apply)
      + repo_digest = (known after apply)
    }

Plan: 2 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.
  ```
Once you confirm, the docker image will be created by Terraform.

```shell
docker_image.nginx: Creating...
docker_image.nginx: Creation complete after 8s [id=sha256:c316d5a335a5cf324b0dc83b3da82d7608724769f6454f6d9a621f3ec2534a5anginx:latest]
docker_container.nginx: Creating...
docker_container.nginx: Creation complete after 1s [id=59e0dd6cc3d890a72ec7dc7af92a1bcc14435cc2efe5b6216e9c37cfafb70106]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
```


To stop the container, run `terraform destroy`.  As with the apply command, you'll be prompted to confirm before the destroy takes place.  Type `yes` and hit ENTER. Terraform will destroy the resources it had created earlier.

```shell
$ terraform destroy

docker_image.nginx: Refreshing state... [id=sha256:c316d5a335a5cf324b0dc83b3da82d7608724769f6454f6d9a621f3ec2534a5anginx:latest]
docker_container.nginx: Refreshing state... [id=e6035a8e128e80127c75620e94d5dddfb687a537669413075763da34907eaed3]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # docker_container.nginx will be destroyed
  - resource "docker_container" "nginx" {
      - attach            = false -> null
      - command           = [
          - "nginx",
          - "-g",
          - "daemon off;",
        ] -> null
      - cpu_shares        = 0 -> null
      - dns               = [] -> null
      - dns_opts          = [] -> null
      - dns_search        = [] -> null
      - entrypoint        = [
          - "/docker-entrypoint.sh",
        ] -> null
      - env               = [] -> null
      - gateway           = "172.17.0.1" -> null
      - group_add         = [] -> null
      - hostname          = "e6035a8e128e" -> null
      - id                = "e6035a8e128e80127c75620e94d5dddfb687a537669413075763da34907eaed3" -> null
      - image             = "sha256:c316d5a335a5cf324b0dc83b3da82d7608724769f6454f6d9a621f3ec2534a5a" -> null
      - init              = false -> null
      - ip_address        = "172.17.0.2" -> null
      - ip_prefix_length  = 16 -> null
      - ipc_mode          = "private" -> null
      - links             = [] -> null
      - log_driver        = "json-file" -> null
      - log_opts          = {} -> null
      - logs              = false -> null
      - max_retry_count   = 0 -> null
      - memory            = 0 -> null
      - memory_swap       = 0 -> null
      - must_run          = true -> null
      - name              = "training" -> null
      - network_data      = [
          - {
              - gateway                   = "172.17.0.1"
              - global_ipv6_address       = ""
              - global_ipv6_prefix_length = 0
              - ip_address                = "172.17.0.2"
              - ip_prefix_length          = 16
              - ipv6_gateway              = ""
              - network_name              = "bridge"
            },
        ] -> null
      - network_mode      = "default" -> null
      - privileged        = false -> null
      - publish_all_ports = false -> null
      - read_only         = false -> null
      - remove_volumes    = true -> null
      - restart           = "no" -> null
      - rm                = false -> null
      - security_opts     = [] -> null
      - shm_size          = 64 -> null
      - start             = true -> null
      - stdin_open        = false -> null
      - storage_opts      = {} -> null
      - sysctls           = {} -> null
      - tmpfs             = {} -> null
      - tty               = false -> null

      - ports {
          - external = 8000 -> null
          - internal = 80 -> null
          - ip       = "0.0.0.0" -> null
          - protocol = "tcp" -> null
        }
    }

  # docker_image.nginx will be destroyed
  - resource "docker_image" "nginx" {
      - id          = "sha256:c316d5a335a5cf324b0dc83b3da82d7608724769f6454f6d9a621f3ec2534a5anginx:latest" -> null
      - latest      = "sha256:c316d5a335a5cf324b0dc83b3da82d7608724769f6454f6d9a621f3ec2534a5a" -> null
      - name        = "nginx:latest" -> null
      - repo_digest = "nginx@sha256:2834dc507516af02784808c5f48b7cbe38b8ed5d0f4837f16e78d00deb7e7767" -> null
    }

Plan: 0 to add, 0 to change, 2 to destroy.

Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

docker_container.nginx: Destroying... [id=e6035a8e128e80127c75620e94d5dddfb687a537669413075763da34907eaed3]
docker_container.nginx: Destruction complete after 1s
docker_image.nginx: Destroying... [id=sha256:c316d5a335a5cf324b0dc83b3da82d7608724769f6454f6d9a621f3ec2534a5anginx:latest]
docker_image.nginx: Destruction complete after 0s

Destroy complete! Resources: 2 destroyed.
```

Congratulations, you've now provisioned and destroyed an NGINX webserver with Terraform and Docker.


## Next Steps

Now that you have created your first infrastructure using Terraform, find more advanced tutorials in the [Terraform Learn Guide](https://learn.hashicorp.com/terraform).

For more detail on the concepts used in this tutorial:

- Read about the Terraform configuration language in the [Terraform documentation](https://www.terraform.io/docs/language/index.html).
- Learn more about Terraform [providers](https://www.terraform.io/docs/providers/index.html).
- Find examples of other uses for Terraform in the documentation [use cases section](https://www.terraform.io/intro/use-cases.html).
- Read the [Docker provider documentation](https://registry.terraform.io/providers/kreuzwerker/docker/latest) to learn more.
- For more information about the terraform state command and subcommands for moving or removing resources from state, see the [CLI `stat`e command documentation](https://www.terraform.io/docs/cli/commands/state/index.html).