# ODK Central Deploy

This deploys an ODK Central instance on an Ubuntu 20.04 server.

## Fresh Deployment

To prepare a new server, the only commands you need to run manually are:

```bash
$ sudo dpkg-reconfigure tzdata && locale-gen en_US.UTF-8
```

## Deploying the Server

Ensure that you have each of the following files: `ssh.cfg`, `ansible.cfg` and `hosts` in the project root folder that configure ansible and specify the deployment nodes.

An example `ssh.cfg`:

```cfg
## This file holds ssh configs for servers targeted by the playbooks
## in this repo.

Host your.hostname.here
    HostName your.hostname.here
    StrictHostKeyChecking=no
    UserKnownHostsFile=/dev/null
    CheckHostIP=no
```

An example `hosts` file:

```text
[all:vars]
ansible_ssh_common_args='-o StrictHostKeyChecking=no -o userknownhostsfile=/dev/null'

[prod]
gateway ansible_host=your.hostname.here ansible_ssh_common_args='-o StrictHostKeyChecking=no -o userknownhostsfile=/dev/null' command_warnings=False

```

An example `ansible.cfg` file:

```cfg
[defaults]
allow_world_readable_tmpfiles = True
inventory = inventory
roles_path = ./libs/
host_key_checking = False
nocows = 1
nocolor = 0
deprecation_warnings=False
sudo_flags = -H -S
timeout = 30
private_key_file = /path.to.your.ssh.private.key

[ssh_connection]
scp_if_ssh = True
pipelining = True
ssh_args = -F ./ssh.cfg -o ControlMaster=auto -o ControlPersist=1800s
```

Secrets are encrypted using Ansible Vault. The encrypted secrets include variables and service account files.

```bash
$ ansible-playbook odk_central.yml --vault-id=~/.vaultpass -vv
```

The decryption password (contents of `~/.vaultpass`) will be communicated to you offline, if needed.
