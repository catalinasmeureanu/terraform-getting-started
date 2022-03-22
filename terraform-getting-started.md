# Getting Started with Terraform

Terraform is the most popular language for defining and provisioning infrastructure as code (IaC).

This tutorial shows how to use Terraform to create an NGINX server running on Docker. 

# Prerequisites

* Terraform CLI version 1.0.0+ installed
* Docker version 20.10.11+


# Install Terraform

To install Terraform, visit [Terraform.io](https://www.terraform.io/downloads.html) and download the appropriate package for your system and unzip it.

# Build infrastructure - Terraform Docker Example

With Terraform installed start creating your infrastructure.


1. We recommend to create a new directory on your local machine for Terraform configuration code.

```shell
$ mkdir terraform-demo
$ cd terraform-demo
```

2. Create a file called `main.tf` for Terraform configuration code.

```shell
$ touch main.tf
```

3. Paste the following content into `main.tf` file and save it:

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

4. Initialize the working directory by running the command `terraform init`. Terraform downloads the necessary provider resources, in this case Docker provider. 

```shell
$ terraform init

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

5. If the initialization is succesfull, provision the resource with the command `terraform apply`:

```shell
$ terraform apply

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

  Enter a value: yes

docker_image.nginx: Creating...
docker_image.nginx: Still creating... [10s elapsed]
docker_image.nginx: Creation complete after 19s [id=sha256:f2f70adc5d89aa922836e9cc6801980a12a7ff9012446cc6edf52ef8798a67bdnginx:latest]
docker_container.nginx: Creating...
docker_container.nginx: Creation complete after 2s [id=89f37c74ac19dba5789869047816d24e597a68c431f58ecb6274ff33668d2c47]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.

```

6. After your approval of the plan, it takes up to a few minutes to create the resources. 

# Destroy Infrastructure

Destroy the infrastructure when you no longer need it:

```shell
$ terraform destroy

docker_image.nginx: Refreshing state... [id=sha256:f2f70adc5d89aa922836e9cc6801980a12a7ff9012446cc6edf52ef8798a67bdnginx:latest]
docker_container.nginx: Refreshing state... [id=89f37c74ac19dba5789869047816d24e597a68c431f58ecb6274ff33668d2c47]

Note: Objects have changed outside of Terraform

Terraform detected the following changes made outside of Terraform since the last "terraform apply":

  # docker_container.nginx has been changed
  ~ resource "docker_container" "nginx" {
      + dns               = []
      + dns_opts          = []
      + dns_search        = []
      + group_add         = []
        id                = "89f37c74ac19dba5789869047816d24e597a68c431f58ecb6274ff33668d2c47"
      + links             = []
      + log_opts          = {}
        name              = "training"
      + storage_opts      = {}
      + sysctls           = {}
      + tmpfs             = {}
        # (31 unchanged attributes hidden)

        # (1 unchanged block hidden)
    }

Unless you have made equivalent changes to your configuration, or ignored the relevant attributes using ignore_changes, the following plan may include actions to undo or respond to these changes.

───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────

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
      - hostname          = "89f37c74ac19" -> null
      - id                = "89f37c74ac19dba5789869047816d24e597a68c431f58ecb6274ff33668d2c47" -> null
      - image             = "sha256:f2f70adc5d89aa922836e9cc6801980a12a7ff9012446cc6edf52ef8798a67bd" -> null
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
          - external = 80 -> null
          - internal = 80 -> null
          - ip       = "0.0.0.0" -> null
          - protocol = "tcp" -> null
        }
    }

  # docker_image.nginx will be destroyed
  - resource "docker_image" "nginx" {
      - id          = "sha256:f2f70adc5d89aa922836e9cc6801980a12a7ff9012446cc6edf52ef8798a67bdnginx:latest" -> null
      - latest      = "sha256:f2f70adc5d89aa922836e9cc6801980a12a7ff9012446cc6edf52ef8798a67bd" -> null
      - name        = "nginx:latest" -> null
      - repo_digest = "nginx@sha256:e1211ac17b29b585ed1aee166a17fad63d344bc973bc63849d74c6452d549b3e" -> null
    }

Plan: 0 to add, 0 to change, 2 to destroy.

Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value:

```

Terraform asks for confirmation. Enter `yes` to destroy all the resources created.

# Next steps

This tutorial shows how to build infrastructure based on an a Terraform configuration file that uses Docker provider.

* More details about [Terraform configuration](https://www.terraform.io/language)
* Lean more about [Terraform providers](https://www.terraform.io/language/providers)
* Read about Terraform commands [init](https://www.terraform.io/cli/commands/init), [apply](https://www.terraform.io/cli/commands/apply), [destroy](https://www.terraform.io/cli/commands/destroy)