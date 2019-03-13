# Consumer ELB Module

This repo contains a Module to deploy a [Elastic Load Balancer (ELB)](https://docs.aws.amazon.com/elasticloadbalancing/latest/classic/introduction.html) on 
[AWS](https://aws.amazon.com) using [Terraform](https://www.terraform.io/). 

![ELB Example](https://docs.aws.amazon.com/elasticloadbalancing/latest/classic/images/internet_facing_load_balancer.png)

This Module works well with the following related modules:

  Consumer EC2 Module
<!---
* [install-vault](https://github.com/hashicorp/terraform-azurerm-vault/tree/master/modules/install-vault): This module can be used to install Vault. It can be used in a 
  [Packer](https://www.packer.io/) template to create a Vault 
  [Azure Manager Image](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/build-image-with-packer).

* [run-vault](https://github.com/hashicorp/terraform-azurerm-vault/tree/master/modules/run-vault): This module can be used to configure and run Vault. It can be used in a 
  [Custom Data](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/classic/inject-custom-data) 
  script to fire up Vault while the server is booting.

* [vault-cluster](https://github.com/hashicorp/terraform-azurerm-vault/tree/master/modules/vault-cluster): Terraform code to deploy a cluster of Vault servers using an [Scale Set]
(https://docs.microsoft.com/en-us/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-create).
   
* [private-tls-cert](https://github.com/hashicorp/terraform-azurerm-vault/tree/master/modules/private-tls-cert): Generate a private TLS certificate for use with a private Vault 
  cluster.
   
* [update-certificate-store](https://github.com/hashicorp/terraform-azurerm-vault/tree/master/modules/update-certificate-store): Add a trusted, CA public key to an OS's 
  certificate store. This allows you to establish TLS connections to services that use this TLS certs signed by this
  CA without getting x509 certificate errors.
-->



## What's a Module?

A Module is a reusable, best-practices definition for how to run a single piece of infrastructure, such 
as a database or server cluster. 

Instead of having to figure out the details of how to run a piece of infrastructure from scratch, you can reuse 
existing code that has been proven in production and approved by the security team.
 
## Who maintains this Module?

This Module is maintained by [Gruntwork](http://www.gruntwork.io/). If you're looking for help or commercial 
support, send an email to [modules@gruntwork.io](mailto:modules@gruntwork.io?Subject=Vault%20Module). 
Gruntwork can help with:

* Setup, customization, and support for this Module.
* Module for other types of infrastructure, such as VPCs, Docker clusters, databases, and continuous integration.
* Module that meet compliance requirements, such as HIPAA.
* Consulting & Training on AWS, Terraform, and DevOps.



## How do you use this Module?

Each Module has the following folder structure:

* [root](https://github.com/hashicorp/terraform-azurerm-vault/tree/master/): The root folder contains an example of running a public Vault cluster on Azure
* [modules](https://github.com/hashicorp/terraform-azurerm-vault/tree/master/modules): This folder contains the reusable code for this Module, broken down into one or more modules.
* [examples](https://github.com/hashicorp/terraform-azurerm-vault/tree/master/examples): This folder contains examples of how to use the modules.
* [test](https://github.com/hashicorp/terraform-azurerm-vault/tree/master/test): Automated tests for the modules and examples.

Click on each of the modules above for more details.

To deploy Vault with this Blueprint, you will need to deploy two separate clusters: one to run 
[Consul](https://www.consul.io/) servers (which Vault uses as a [high availability 
backend](https://www.vaultproject.io/docs/concepts/ha.html)) and one to run Vault servers. 

To deploy the Consul server cluster, use the [Consul Azure Module](https://github.com/hashicorp/terraform-azurerm-consul). 

To deploy the Vault cluster:

1. Create an Azure Image that has Vault installed (using the [install-vault module](https://github.com/hashicorp/terraform-azurerm-vault/tree/master/modules/install-vault)) and the Consul
   agent installed (using the [install-consul 
   module](https://github.com/hashicorp/terraform-azurerm-consul/tree/master/modules/install-consul)). Here is an 
   [example Packer template](https://github.com/hashicorp/terraform-azurerm-consul/tree/master/examples/consul-image). 
   
1. Deploy that Azure Image across a Scale Set in a private subnet using the Terraform [vault-cluster 
   module](https://github.com/hashicorp/terraform-azurerm-vault/tree/master/modules/vault-cluster). 

1. Execute the [run-consul script](https://github.com/hashicorp/terraform-azurerm-consul/tree/master/modules/run-consul)
   with the `--client` flag during boot on each Instance to have the Consul agent connect to the Consul server cluster. 

1. Execute the [run-vault](https://github.com/hashicorp/terraform-azurerm-vault/tree/master/modules/run-vault) script during boot on each Instance to create the Vault cluster. 

1. If you only need to access Vault from inside your Azure account (recommended), run the [install-dnsmasq 
   module](https://github.com/hashicorp/terraform-azurerm-consul/tree/master/modules/install-dnsmasq) on each server, and 
   that server will be able to reach Vault using the Consul Server cluster as the DNS resolver (e.g. using an address 
   like `vault.service.consul`). See the [main example](https://github.com/hashicorp/terraform-azurerm-consul/tree/master/MAIN.md) for working 
   sample code.

1. Head over to the [How do you use the Vault cluster?](https://github.com/hashicorp/terraform-azurerm-vault/tree/master/modules/vault-cluster#how-do-you-use-the-vault-cluster) guide
   to learn how to initialize, unseal, and use Vault.

 
## How do I contribute to this Module?

Contributions are very welcome! Check out the [Contribution Guidelines](https://github.com/hashicorp/terraform-azurerm-vault/tree/master/CONTRIBUTING.md) for instructions.



## How is this Module versioned?

This Module follows the principles of [Semantic Versioning](http://semver.org/). You can find each new release, 
along with the changelog, in the [Releases Page](../../releases). 

During initial development, the major version will be 0 (e.g., `0.x.y`), which indicates the code does not yet have a 
stable API. Once we hit `1.0.0`, we will make every effort to maintain a backwards compatible API and use the MAJOR, 
MINOR, and PATCH versions on each release to indicate any incompatibilities. 



# AWS Elastic Load Balancer (ELB) Terraform module

Terraform module which creates ELB resources on AWS.

These types of resources are supported:

* [ELB](https://www.terraform.io/docs/providers/aws/r/elb.html)
* [ELB Attachment](https://www.terraform.io/docs/providers/aws/r/elb_attachment.html)

Root module calls these modules which can also be used separately to create independent resources:

* [elb](https://github.com/terraform-aws-modules/terraform-aws-elb/tree/master/modules/elb) - creates ELB
* [elb_attachment](https://github.com/terraform-aws-modules/terraform-aws-elb/tree/master/modules/elb_attachment) - creates ELB attachment

## Usage

```hcl
module "elb_http" {
  source = "terraform-aws-modules/elb/aws"

  name = "elb-example"

  subnets         = ["subnet-12345678", "subnet-87654321"]
  security_groups = ["sg-12345678"]
  internal        = false

  listener = [
    {
      instance_port     = "80"
      instance_protocol = "HTTP"
      lb_port           = "80"
      lb_protocol       = "HTTP"
    },
  ]

  health_check = [
    {
      target              = "HTTP:80/"
      interval            = 30
      healthy_threshold   = 2
      unhealthy_threshold = 2
      timeout             = 5
    },
  ]

  access_logs = [
    {
      bucket = "my-access-logs-bucket"
    },
  ]

  // ELB attachments
  number_of_instances = 2
  instances           = ["i-06ff41a77dfb5349d", "i-4906ff41a77dfb53d"]
  
  tags = {
    Owner       = "user"
    Environment = "dev"
  }
}
```

## Examples

* [Complete ELB example](https://github.com/terraform-aws-modules/terraform-aws-elb/tree/master/examples/complete)

## Known Issues/Limitations

* Support for HTTPS is a common requirment for Internet facing ELBs. However, there is a known Terraform limitation with providing a dynamic computed value for the ```ssl_id_certificate``` parameter in nested block structures (Refer to: https://github.com/hashicorp/terraform/issues/16582#issuecomment-342570913).

<!-- BEGINNING OF PRE-COMMIT-TERRAFORM DOCS HOOK -->

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|:----:|:-----:|:-----:|
| access_logs | An access logs block | list | `<list>` | no |
| connection_draining | Boolean to enable connection draining | string | `false` | no |
| connection_draining_timeout | The time in seconds to allow for connections to drain | string | `300` | no |
| cross_zone_load_balancing | Enable cross-zone load balancing | string | `true` | no |
| health_check | A health check block | list | - | yes |
| idle_timeout | The time in seconds that the connection is allowed to be idle | string | `60` | no |
| instances | List of instances ID to place in the ELB pool | list | `<list>` | no |
| internal | If true, ELB will be an internal ELB | string | - | yes |
| listener | A list of listener blocks | list | - | yes |
| name | The name of the ELB | string | - | yes |
| number_of_instances | Number of instances to attach to ELB | string | `0` | no |
| security_groups | A list of security group IDs to assign to the ELB | list | - | yes |
| subnets | A list of subnet IDs to attach to the ELB | list | - | yes |
| tags | A mapping of tags to assign to the resource | string | `<map>` | no |

## Outputs

| Name | Description |
|------|-------------|
| this_elb_arn | The ARN of the ELB |
| this_elb_dns_name | The DNS name of the ELB |
| this_elb_id | The name of the ELB |
| this_elb_instances | The list of instances in the ELB |
| this_elb_name | The name of the ELB |
| this_elb_source_security_group_id | The ID of the security group that you can use as part of your inbound rules for your load balancer's back-end application instances |
| this_elb_zone_id | The canonical hosted zone ID of the ELB (to be used in a Route 53 Alias record) |

<!-- END OF PRE-COMMIT-TERRAFORM DOCS HOOK -->

## Authors

Module managed by [HashiCorp SE Team](https://github.com/hashicorp).

## License

Apache 2 Licensed. See LICENSE for full details.


