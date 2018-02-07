Ansible playbook for sunggun-yu.openstack-bootstrap
========================================================================================================================

Ansible playbook for the general OpenStack provisioning workflow. 

please refer to https://galaxy.ansible.com/sunggun-yu/openstack-bootstrap for more detail about role variables.

Usage
------------------------------------------------------------------------------------------------------------------------


### Install the Ansible role `sunggun-yu.openstack-bootstrap`

```bash
ansible-galaxy install sunggun-yu.openstack-bootstrap
```
or,
```bash
ansible-galaxy install \
 --role-file=requirements.yml
```

### Prepare role variable file for your openstack specs
To run the playbook/role, role variable is required. you can set `group_vars` in this playbook directory, or you can have variable file outside of playbook. 

/some/file.yml
```yaml
openstack_bootstrap_specs:
  - name: instance-name-01
    ansible:
      inventory_groups:
        - group_name
      ssh_user: "ssh_user_name"
      ssh_key: "~/.ssh/your_private_key_file"
    os_auth:
      auth_url: "https://openstack-auth-url-dc-1"
      username: "your-name"
      password: "your-password"
      project_name: "your-project-01"
    os_server:
      key_name: "your-openstack-key-name"
      availability_zone: zone-a
      flavor: m1.large
      image: image-name-or-id
      security_groups:
        - security-group-name
      nics:
        - net-id: network-id
    volumes:
      - name: volume-name-01-01
        os_volume:
          volume_type: ceph
          size: 200
          availability_zone: nova
        os_server_volume:
          device: /dev/vdb
        mount:
          fstype: ext4
          path: /path/mount/01
      - name: volume-name-01-02
        os_volume:
          volume_type: ceph
          size: 200
          availability_zone: nova
        os_server_volume:
          device: /dev/vdc
        mount:
          fstype: ext4
          path: /path/mount/02
```

### Run the playbook

Execute `ansible-playbook` cli with following options.

```bash
ansible-playbook \
 -l localhost \
 --extra-vars=@/some/file.yml \
 bootstrap.yml 
```

In case if you want to separate variable file for `ansible-vault` to protect openstack auth information.

Ansible vault variable file: /some/encrypted.yml
```yaml
openstack_auth_username: user-name
openstack_auth_password: user-password
```

Ansible variable file: /some/file.yml
```yaml
openstack_bootstrap_specs:
  - name: instance-name-01
    ansible:
      inventory_groups:
        - group_name
      ssh_user: "ssh_user_name"
      ssh_key: "~/.ssh/your_private_key_file"
    os_auth:
      auth_url: "https://openstack-auth-url-dc-1"
      username: "{{ openstack_auth_username }}"
      password: "{{ openstack_auth_password }}"
      project_name: "your-project-01"
    os_server:
      key_name: "your-openstack-key-name"
      availability_zone: zone-a
      flavor: m1.large
      image: image-name-or-id
      security_groups:
        - security-group-name
      nics:
        - net-id: network-id
```

Run the playbook:
```bash
ansible-playbook \
 -l localhost \
 --extra-vars=@/some/file.yml \
 --extra-vars=@/some/encrypted.yml \
 --ask-vault-pass \
 bootstrap.yml 
```

### `ansible-playbook` usage.

```
Usage: ansible-playbook [options] bootstrap.yml [playbook2 ...]
```
Options:
- `-l`: `-l SUBSET, --limit=SUBSET`, further limit selected hosts to an additional pattern
- `--extra-vars`: set additional variables as key=value or YAML/JSON, if filename prepend with @
- `--ask-vault-pass`: ask for vault password

License
------------------------------------------------------------------------------------------------------------------------

MIT

Author Information
------------------------------------------------------------------------------------------------------------------------

Sunggun Yu (<sunggun.dev@gmail.com>)
