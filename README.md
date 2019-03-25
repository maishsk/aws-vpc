# aws-vpc

Will create a VPC in the specified AWS region. This role will also attach an Internet Gateway to the VPC.

## Requirements

AWS credentials and the correct permissions to create the resources

## Role Variables

The variables uses in this role are

| Variable Name | Required | Description | 
|----|----|----|
| `region`| **Yes** | The region that you will deploy into |
| `vpc_name` | **Yes** | The name of the VPC | 
| `vpc_cidr_block` | **Yes** | The CIDR block of VPC  |
| `enable_dns_support` | Optional | Enable DNS Support<br> - Default `true` |
| `enable_dns_hostnames` | Optional | Enable AWS DNS support<br> - Default `true` |
| `vpc_multi_ok` | Optional | Create new VPC if another VPC with the same name and CIDR block exists? <br> - Default `no` |
| `vpc_tenancy` | Optional | Default or dedicated tenancy<br> - Default `default` |
| `aws_tags` | Optional | List of tags to use during deployment (see example below) |

## Dependencies

None

## Example Playbook

### Download dependencies

#### Create requirements file

Create a `requirements.yml` file with the following contents
```
- src: https://github.com/maishsk/aws-vpc
  version: master
```

#### Download dependencies
Run the following command:
```
ansible-galaxy install -r requirements.yml --force -p .
```

### Create playbook
Create a `main.yaml` file with the following contents:
```
---
- name: VPC Provisioning
  hosts: localhost
  connection: local
  gather_facts: false
  vars_files:
    - vars.yml

  tasks:
  - name: Create Process
    include_role:
      name: "{{ item }}"
    with_items:
      - aws-vpc
    tags: [ 'never', 'create' ]

  - name: Rollback Process
    include_role:
      name: "{{ item }}"
    with_items:
      - aws-vpc
    tags: [ 'never', 'rollback' ]
```

Create a `vars/vars.yml` with the content similar to:
```
vpc_name: vpc_test
vpc_cidr_block: 192.168.100.0/24
region: us-east-2
aws_tags:
 - foo: bar
 - blah: meh
 - ami: 3syllables
```

## Running the playbook

To create the VPC

`ansible-playbook main.yml --tags create`

To remove the VPC

`ansible-playbook main.yml --tags rollback`

## License

BSD

## Author Information
This role was created by [Maish Saidel-Keesing](https://www.maishsk.com/), author of [The Cloud Walkabout](http://cloudwalkabout.com/).