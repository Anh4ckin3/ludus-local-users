# Ansible Role: Local users and groups (Ludus)

An Ansible Role that creates local users on Windows or Linux and manages local groups.

The role performs the following actions:
- Create Linux users
- Add Linux users to groups (when creating a user)
- Configure passwordless sudo configuration
- Create Windows users
- Add Windows users to groups

> [!WARNING]
> At the moment, the role does not support modifying group membership of already existing Linux users.

## Requirements

None.

## Role Variables

Available variables are listed below. There is no default values. Everything shall be configured in the range configuration.

### Variables used when creating a user :

    # User login
    ludus_local_users.login: jdoe

    # User password
    ludus_local_users.password: aA8MaQBCtBtPYAFh

    # Optional (Linux Only): Groups to put the user into. In case of multiple groups, user comma separated list
    ludus_local_users.groups: sudo,adm

    # Optional (Linux Only): Whether to set NOPASSWD in sudoers configuration for the created Linux user
    ludus_local_users.sudo_nopasswd: true

### Variables used when modifying local groups (Windows Only)

    # Group to modify
    ludus_local_users.name: jdoe

    # List of accounts or groups to add to the local group
    ludus_local_users.members: 
      - my_local_user
      - MYRANGE\Developers
      - MYRANGE\Tier 1 Admins

## Example Ludus Range Config

### Linux VM

```yaml
  - vm_name: "{{ range_id }}-GIT-01"
    hostname: "{{ range_id }}-GIT-01"
    template: debian-12-x64-server-template
    vlan: 55
    ip_last_octet: 2
    dns_rewrites: 
      - git.myrange.corp
    ram_gb: 8
    cpus: 2
    linux: true
    testing:
      snapshot: true
      block_internet: false
    roles:
      - ludus_local_users
    role_vars:
      ludus_local_users:
        - login: jdoe
          password: aA8MaQBCtBtPYAFh
          groups: sudo
          sudo_nopasswd: true
        - login: msmith
          password: 25H60eORSggFfH2Y
```

### Windows VM

```yaml
    hostname: "{{ range_id }}-TSE-01"
    template: win2016-server-x64-template
    vlan: 53
    ip_last_octet: 5
    ram_gb: 8
    cpus: 4
    windows:
      sysprep: true
    domain:
      fqdn: myrange.corp
      role: member
    testing:
      snapshot: true
      block_internet: true
    roles:
      - ludus_local_users
    role_vars:
      ludus_local_users:
        - login: local-adm
          password: 5tB2RgjjI7kdEXHC
      ludus_local_groups:
        - name: Administrators
          members:
            - local-adm
        - name: Remote Desktop Users
          members:
            - MYRANGE\Developers
            - MYRANGE\Tier 1 Admins
```

## License

GPLv3

## Author Information

This role was created by [Cyblex Consulting](https://github.com/Cyblex-Consulting), for [Ludus](https://ludus.cloud/).