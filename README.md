# aws-ansible

Use ansible to deploy RHEL8 on ARM in AWS!  Includes registering with subscription-manager and setting up container support.

## Prereqs

* Install and configure awscli
* Install ansible
* Disable `host_key_checking` in `~/.ansible.cfg`
  ```
  [defaults]
  host_key_checking = False
  ```

## Create inventory file

This inventory file will simply hold info for remote access to the aws instances.  The group itself will be dynamically managed.
```
[aws_launched]

[aws_launched:vars]
ansible_ssh_private_key_file=/path/to/your/aws/ssh/key
ansible_ssh_user=ec2-user
```

## Create vault

Use `ansible-vault` to create a vault to hold your subscription-manager secrets.

```
mkdir -p group_vars/aws_launched
ansible-vault create group_vars/aws_launched/vault
```

Then fill in the vault with your own secrets:

```
---
vault_rhsm_user: <Your Red Hat user>
vault_rhsm_password: <Your Red Hat password>
vault_rhsm_pool_id: <Your RHEL subscription pool ID>
```

## Fill in instance vars

```
mkdir host_vars
```

Create `localhost.yml` file under `host_vars/` and fill it with:

```
---
inst_name: <instance name>
keypair: <aws ssh keypair>
instance_type: <for example m6g.large>
security_group: <aws security group>
image_id: <for example ami-01fc429821bf1f4b4>
region: <for example us-east-1>
vpc_subnet_id: <aws vpc subnet id>
```

## Deploy instance

To deploy an instance:
```
ansible-playbook deploy_arm_instance.yml -i inventory --ask-vault-pass
```
