# Ansible Bootmedia Role

## About 

This role creates bootmedia in ISO format that
are ready to be used for installation of new vms and hosts. 

There are three categories of files that are shipped with the bootmedia:

1. A setup file, that performs unattended network configuration
in an early stage. For linux this is a customised `isolinux` configuration file.
When `UEFI` boot is enabled, a `grub` config file with similar content is created.

2. For  installation media, a setup file is shipped that contains the information for an
unattended information. For Redhat this is known as kickstart file.

3. An additional file is shipped that contains files to perform authentication so
that other tasks. For linux this is a ssh public key. For linux this is
a tarball that gets extracted to the root filesystem when installation is
finished.

It is possible to create 3 flavors of bootmedia that differ in size:

- `full`: a bootmedia that ships the original content and the setup file
- `minimal`: a bootmedia that ships the install image (in Redhat called
  2ndstage). This does not ship any packages
- `tiny`: A bootmedia that only ships the bootloader and the setup

Features:
- Download the bootmedia, extract to a folder
- Create ready to be used kickstart, isolinux files

## Howto Use

Inventory Example:

inventory:
```
[bootmedia_target]
test1
test2

[bootmedia_host]
localhost
```

Example data (full content):

host_vars/myhost:
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

Playbook Example:

prepare_bootmedia:
```
---
# gather facts from bootmedia_host
- hosts: bootmedia_host
- hosts: bootmedia_target
  gather_facts: false
  tasks:
    - import_role: 
        name: bootmedia
```



Example run:

```
ansible-playbook -i inventory prepare_bootmedia.yml
```

# FAQ

Can I use `extra-vars` pointing to the `bootmedia_data` data?

The role makes use of `set_fact`. 
In recent ansible(>2.4) is an issue with variable precedence 
when using `set_fact` and `--extra-vars` together.

So it is recommended to put the `bootmedia_data` into `host_vars` or directly
into the `inventory` file as explained in the docs [1].

```
ansible-playbook -i inventory bootmedia_prepare.yml -e@~/centos-full.yml
```

[1] https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html#id12
