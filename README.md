This role creates bootmedia in ISO format that
are ready to be used for installation of new hosts. 

Features:
- Download the bootmedia, extract to a folder
- Create ready to be used kickstart, isolinux files

Inventory Example:

```
[bootmedia_target]
test1
test2

[bootmedia_host]
localhost bootmedia_parent_path=/data/isos
```

Playbook Example:

```
---
- hosts: bootmedia_host
- hosts: bootmedia_target
  gather_facts: false
  tasks:
    - import_role: 
        name: bootmedia_prepare
```

Example data (full content):

```
bootmedia_data:
  language: en_US
  locale: de_DE
  timezone: Europe/Berlin
  full_content: true
  arch: x86_64
  system: linux
  distribution: centos
  efiboot: true
  initial_password: mypassword
  bootdisk: vda
  networks:
    - admin: true
      dhcp: false
      domain: local.lab
      address: 192.168.122.10
      netmask: 255.255.255.0
      gateway: 192.168.122.1
      dns: 192.168.122.1
      mac: 52:54:00:a3:48:7c
```
