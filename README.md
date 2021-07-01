# terraform-aws-ec2-multi-nics-disks
Deploy EC2 Servers with Multi-Disk and/or Multi-NICs

## Features

This module will allow you to create a EC2 Server with a multitude of different features:
* Deploy a simple EC2 Server
* Deploy a EC2 server with multiple interfaces by simply enabling true and providing data 
* Deploy a EC2 sever with multiple disks by simply enabling true and providing data
* Deploy a EC2 server with a dedicated host (Use dedicated_host module on Terraform Public Registry refer to [Anton Babenko](https://github.com/antonbabenko))

# Usage

```hcl
module "linux_rhel7" {
  source = ""

  name                   = "my-linux-server"
  instance_count         = 1
  ami                    = "ami-0f88a64c734438fa0"
  instance_type          = "t2.micro"
  vpc_security_group_ids = ["sg-xxxxx"]
  subnet_id  = "subnet-xxxxxxxxxx"
  private_ip = ["172.31.1.10"]
  key_name = "key_pair_name"
  root_volume_type = "gp2"
  root_volume_size = 20
  
  #############################################################################################################################
  # Attaching multiple volumes to a EC2 server if set to true enter how many volumes, volumes names, and the size of each volume
  #############################################################################################################################

  multi_volume_instance = true
  ebs_volume_count = 3
  ebs_device_names = ["/dev/sdb","/dev/sdc","/dev/sdd"]
  ec2_ebs_volume_size = [70, 40, 10]

  #############################################################################################################################
  # Attaching multi interfaces provide # of interfaces and IPs - Note will use the Security Asscioated in vpc_security_group_ids
  #############################################################################################################################

  multi_interface_instance = true
  ec2_interface_count = 2
  ec2_interface_ip = ["172.31.1.30", "172.31.1.60"]
  ec2_interface_subnet_ids = ["subnet-xxxx", "subnet-xxxx"]

  #############################################################################################################################
  # Attach instance to dedicated host
  #############################################################################################################################

  # Dedicated Host
  host_id = module.<module_name_here>.dedicated_hosts["HostID"]
  tenancy = "host"

  custom_tags = {
    Terraform   = "True"
    Environment = "development"
    Application = "My Lab Server deployed with Terraform"
  }
}
```

<!-- BEGINNING OF PRE-COMMIT-TERRAFORM DOCS HOOK -->

## Requirements

No requirements.

## Providers

| Name | Version |
|------|---------|
| aws | n/a |

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| <a name="name"></a> [name](#name) | Name to be used on all the resources as identifier | `string`  | n/a | no |
| <a name="instance_count"></a> [instance\_count](#instance\_count) | How many instances to deploy if using multiple disk and/or IPs keep at 1 | `number` | `1` | no |
| <a name="ami"></a> [ami](#ami) |  ID of AMI to use for the instance | `string` | n/a | yes | n/a |
| <a name="instance_type"></a> [instance\_type](#instance\_type) |  Instance type to be used - t2.micro, c5.2xlarge, etc | `string` | n/a | yes |
| <a name="vpc_security_group_ids"></a> [vpc\_security\_group\_ids](#vpc\_security\_group\_ids) |  Security Groups | `list(string)` | n/a | yes |
| <a name="subnet_id"></a> [subnet\_id](#subnet\_id) | Subnet ID Instance will run in | `string` | `"subnet-068e2114e4b5cf5ed"` | yes |
| <a name="root_volume_type"></a> [root\_volume\_type](#root\_volume\_type) | Volume type for root volume gp2, gp3, i2, etc) | `string` | `"gp2"` | no |
| <a name="root_volume_size"></a> [root\_volume\_size](#root\_volume\_size) | Volume Size for root volume | `number` | `30` | no |
| <a name="root_block_device"></a> [root\_block\_device](#root\_block\_device) | Root EBS Volume Allows you to set drive name, size, and type | `list(map(string))` | `[]` | no |
| <a name="key_name"></a> [key\_name](#key\_name) | The key name to use for the instance | `string` | `""` | no |
| <a name="host_id"></a> [host\_id](#host\_id) | Dedicated Host ID | `string` | `null` | no |
| <a name="tenancy"></a> [tenancy](#tenancy) | The tenancy of the instance (if the instance is running in a VPC). Available values: default, dedicated, host. | `string` | `"default"` | no |
| <a name="custom_tags"></a> [custom\_tags](#custom\_tags) | A mapping of tags to assign to the resource | `map(string)` | `{}` | no |
| <a name="private_ip"></a> [private\_ip](#private\_ip) | Private IP address to associate with the instance in a VPC | `list(string)` | `null` | no |
| <a name="private_ips"></a> [private\_ips](#private\_ips) | A list of private IP address to associate with the instance in a VPC. Should match the number of instances. | `list(string)` | `null` | no |
| <a name="secondary_private_ips"></a> [secondary\_private\_ips](#secondary\_private\_ips) | A list of secondary private IPv4 addresses to assign to the instance's primary network interface (eth0) in a VPC | `list(string)` | `null` | no |
| <a name="multi_volume_instance"></a> [multi\_volume\_instance](#multi\_volume\_instance) | If instance needs multiple Volumes/Disk enable this option and pass the volume names and number of volumes needed | `bool` | `false` | no |
| <a name="ebs_device_names"></a> [ebs\_device\_names](#ebs\_device\_names) | Volume names example: /dev/sda or xvfa. **Depends on [multi\_volume\_instance](#multi\_volume\_instance) = true** | `list(string)` | `["/dev/sdd","/dev/sde"]` | no |
| <a name="ebs_volume_count"></a> [ebs\_volume\_count](#ebs\_volume\_count) | How many Volumes do you want to create. **Depends on [multi\_volume\_instance](#multi\_volume\_instance) = true** | `number` | `2` | no |
| <a name="ec2_ebs_volume_size"></a> [ec2\_ebs\_volume\_size](#ec2\_ebs\_volume\_size) | Enter the Volume size for each volume in order. **Depends on [multi\_volume\_instance](#multi\_volume\_instance) = true** | `list(number)` | `[50,50]` | no |
| <a name="multi_interface_instance"></a> [multi\_interface\_instance](#multi\_interface\_instance) | If instance needs multiple Interfaces enable this option and pass the interface IP |  `bool` | `false` | no |
| <a name="ec2_interface_count"></a> [ec2\_interface\_count](#ec2\_interface\_count) | How many ENIs you want to create **Depends on [multi\_interface\_instance](#multi\_interface\_instance) = true** | `number` | `2` | no |
| <a name="ec2_interface_ip"></a> [ec2\_interface\_ip](#ec2\_interface\_ip) | ENIs allow you to apply multiple IPs to one interface or you can past a list of IPs to for loop and create multiple interfaces. **Depends on [multi\_interface\_instance](#multi\_interface\_instance) = true** | `list(list(string))` | `[["10.0.0.50"],["10.0.0.65"]]` | no |
| <a name="ec2_interface_subnet_ids"></a> [ec2\_interface\_subnet\_ids](#ec2\_interface\_subnet\_ids) | "Subnets IDs for each interface in order" **Depends on [multi\_interface\_instance](#multi\_interface\_instance) = true** | `list(string)` | `["subnet-xxx","subnet-xxx"]` | no |
| <a name="device_index"></a> [device\_index](#device\_index) | Integer to define the devices index, 0 is the default device_index/primary interface **Depends on [multi\_interface\_instance](#multi\_interface\_instance) = true** | `number` | `n/a (handle with simple math using interface list` | no |

## Outputs

| Name | Description |
|------|-------------|
| instance_id | Instance ID |

<!-- END OF PRE-COMMIT-TERRAFORM DOCS HOOK -->

## Authors

Module managed by [Joel Gray](https://github.com/pyjoepy06).

## License

Apache 2 Licensed. See LICENSE for full details.