Ludus Role: Local users and groups
=========

This is a role for Ludus that manages local users and groups for Windows or Linux

The role performs the following actions:
- Create Linux users
- Add Linux users to groups
- Configure passwordless sudo configuration
- Create Windows users
- Add Windows users to groups


Role Variables
--------------

There is no default values. Everything shall be configured in the range configuration.

A full Linux VM may look like this in the range configuration:

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
      - local-users
    role_vars:
      local_users:
        - login: jdoe
          password: aA8MaQBCtBtPYAFh
          groups: sudo
          sudo_nopasswd: true
        - login: msmith
          password: 25H60eORSggFfH2Y
```

A full Windows VM may look like this in the range configuration:

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
      - local-users
    role_vars:
      local_users:
        - login: local-adm
          password: 5tB2RgjjI7kdEXHC
      local_groups:
        - name: Administrators
          members:
            - local-adm
        - name: Remote Desktop Users
          members:
            - MYRANGE\Developers
            - MYRANGE\Tier 1 Admins
```