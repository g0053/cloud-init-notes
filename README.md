### General Information about Cloud-Config
The cloud-config format implements a declarative syntax for many common configuration items, making it easy to accomplish many tasks. It also allows you to specify arbitrary commands for anything that falls outside of the predefined declarative capabilities.

This "best of both worlds" approach lets the file acts like a configuration file for common tasks, while maintaining the flexibility of a script for more complex functionality.

## YAML Formatting
The file is written using the YAML data serialization format. The YAML format was created to be easy to understand for humans and easy to parse for programs.

YAML files are generally fairly intuitive to understand when reading them, but it is good to know the actual rules that govern them.

Some important rules for YAML files are:

- Indentation with whitespace indicates the structure and relationship of the items to one another. Items that are more indented are sub-items of the first item with a lower level of indentation above them.
- List members can be identified by a leading dash.
- Associative array entries are created by using a colon (:) followed by a space and the value.
- Blocks of text are indented. To indicate that the block should be read as-is, with the formatting maintained, use the pipe character (|) before the block.

```
#cloud-config
users:
  - name: demo
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
     - ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC2UWeC22g/ARHkXkv3Sj64E5s6DwVLMVxxLiKNvRjUDKurnZAewRC8EoytmD/Ky4/RQA8715K89RzVjc3G2J2m7DVfGjWCpfJ9fw5hf9oNscph+y5i9VcLQ6pKWrSee7rRm8Z41aZMIkF8X5Qg/QYyJ8WP1ok8qlD39j3JzUmgS1e2zJDKVKgCGves5zY7CBtJ4NxUrvR91ZFzFVXxj+sjdT/BYkndxdCKxjynxEaq2uFeI00IiNsXLrv4cf9CJIuCdbL/Op3Gq/2NFpXXW6WV7/milXFXL9RunuMJbU0197/Y0vLqhxAKB+o+7ZqV6NK5V2jb6gMpsENZPYxfJZrh gus@belomor.nl
     - ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDWOOxN3Y8u8sFH026HYWdBh3vJ0InBRM9Az38KbTPoVgkVUhPB4i1a75/wJhdXfunf9/ZwoKRLz1F/LvJw9IETDZ52AZGFyuL/Z3toBQ0yAR8B5ELrtfgLGoch+iIrUMJYcUjYYEk+IAw2E5uuauKhOa09QNP8Np4Xk9zHcKCDi2S1Dqy1ePbTLmPyGLynQfdJsoiuZik4ddOmZgEGQLiU3VpXW8x3ICFydgEIXHy8+NjOyU6l7cHmYKaOnLgILdyWNv1K6QC1zmwwtZ6isQIXzzX8Gypb7vNUFzbDOlTj3aydytI0R3dUdqbdyCGoXRaMb+RNqq9mo5b13FAdZib7 hd@belomor.nl
     runcmd:
  - touch /test.txt
  ```
## User and Group Management
```
#cloud-config
users:
  - first_user_parameter
    first_user_parameter

  - second_user_parameter
    second_user_parameter
    second_user_parameter
    second_user_parameter
    
#cloud-config
groups:
  - group1
  - group2: [user1, user2]
```
## Change Passwords for Existing Users
```
#cloud-config
chpasswd:
  list: |
    user1:password1
    user2:password2
    user3:password3
  expire: False
```

## Write Files to the Disk with contend:
```
#cloud-config
write_files:
  - path: /test.txt
    content: |
      Here is a line.
      Another line is here.
      
 ```
 ## Configure resolv.conf to Use Specific DNS Servers
 
 ```
 #cloud-config
manage-resolv-conf: true
resolv_conf:
  nameservers:
    - 'first_nameserver'
    - 'second_nameserver'
  searchdomains:
    - first.domain.com
    - second.domain.com
  domain: domain.com
  options:
    option1: value1
    option2: value2
    option3: value3
```
## Shutdown or Reboot the Server

```
#cloud-config
power_state:
  timeout: 120
  delay: "+5"
  message: Rebooting in five minutes. Please save your work.
  mode: reboot
```


# The modules that run in the 'init' stage
cloud_init_modules:
 - migrator
 - ubuntu-init-switch
 - seed_random
 - bootcmd
 - write-files
 - growpart
 - resizefs
 - set_hostname
 - update_hostname
 - update_etc_hosts
 - ca-certs
 - rsyslog
 - users-groups
 - ssh
 
# The modules that run in the 'config' stage
cloud_config_modules:
## Emit the cloud config ready event this can be used by upstart jobs for 'start on cloud-config'.
 - emit_upstart
 - disk_setup
 - mounts
 - ssh-import-id
 - locale
 - set-passwords
 - snappy
 - grub-dpkg
 - apt-pipelining
 - apt-configure
 - package-update-upgrade-install
 - fan
 - landscape
 - timezone
 - lxd
 - puppet
 - chef
 - salt-minion
 - mcollective
 - disable-ec2-metadata
 - runcmd
 - byobu

## The modules that run in the 'final' stage
cloud_final_modules:
 - rightscale_userdata
 - scripts-vendor
 - scripts-per-once
 - scripts-per-boot
 - scripts-per-instance
 - scripts-user
 - ssh-authkey-fingerprints
 - keys-to-console
 - phone-home
 - final-message
 - power-state-change
 
``` 
#cloud-config 
....
coreos:
  units:
    - name: runcmd.service
      command: start
      content: |
        [Unit]
        Description=Creates a tmp foo file

        [Service]
        Type=oneshot
        ExecStart=/bin/sh -c "touch /tmp/foo;"
        
        
 ```
 ```
 #cloud-config 
.... 
coreos:
  units:
    - name: update-sysctl.service
      command: start
      content: |
        [Unit]
        Description=Update sysctl values written by cloud-config
        [Service]
        ExecStart=/usr/lib/systemd/systemd-sysctl ...
        
 ```
 ## run arbitrary commands similar to runcmd.
 ```
 - name: runcmd.service
  command: start
  content: |
    [Unit]
    Description=Runs a command

    [Service]
    Type=oneshot
    ExecStart=/bin/sh -c "touch /etc/environment;"
```
## Run commands on first boot

```
#cloud-config

# boot commands
# default: none
# this is very similar to runcmd, but commands run very early
# in the boot process, only slightly after a 'boothook' would run.
# bootcmd should really only be used for things that could not be
# done later in the boot process.  bootcmd is very much like
# boothook, but possibly with more friendly.
# - bootcmd will run on every boot
# - the INSTANCE_ID variable will be set to the current instance id.
# - you can use 'cloud-init-per' command to help only run once
bootcmd:
 - echo 192.168.1.130 us.archive.ubuntu.com >> /etc/hosts
 - [ cloud-init-per, once, mymkfs, mkfs, /dev/vdb ]
 ```
 ```
 

#cloud-config

# run commands
# default: none
# runcmd contains a list of either lists or a string
# each item will be executed in order at rc.local like level with
# output to the console
# - runcmd only runs during the first boot
# - if the item is a list, the items will be properly executed as if
#   passed to execve(3) (with the first arg as the command).
# - if the item is a string, it will be simply written to the file and
#   will be interpreted by 'sh'
#
# Note, that the list has to be proper yaml, so you have to quote
# any characters yaml would eat (':' can be problematic)
runcmd:
 - [ ls, -l, / ]
 - [ sh, -xc, "echo $(date) ': hello world!'" ]
 - [ sh, -c, echo "=========hello world'=========" ]
 - ls -l /root
 # Note: Don't write files to /tmp from cloud-init use /run/somedir instead.
 # Early boot environments can race systemd-tmpfiles-clean LP: #1707222.
 - mkdir /run/mydir
 - [ wget, "http://slashdot.org", -O, /run/mydir/index.html ]
 ```
 ## API sample data payload may look like this:
```
{"name": "your_vm_name",
"private_networking": true,
"region": "DC1",
"size": "512mb",
"image": "ubuntu-18-04-x64",
"user-data": "#cloud-config
users:
  - name: demo
    ssh-authorized-keys:
      - ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEApABUw57Esm7r0Cx4xYKnzVD6wlcGeAi4bF3s2/vVsQlwba2B/268f1sItW0+vjt6YwRTn8CQqX/A0gDP+rSVa9bXJAYd3jQjNQrtFkMpFTx03R0u7Mz/ZaQAuZCb3QIKs/qYKw7mNdhpcucHcTKOkbCB9iUVXGxS9RP3X3YvNgpycfV+5rnOq6cPd1Xar3IJ6ElgGCPeSdaZICXoIJN+rS5uxyTAxuHiSNyOaxUZkKW5XpAM72YvpLG21fv5hPYP548dV4arAdnWqv2EdQDQNmI0b/ZGLBjeJavj2e24AdG+OnKeJSpRqfGMMXpPLvibD1xtL3fskmq6LNKEU2vNeQ== gus@belomor.nl
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    groups: sudo
    shell: /bin/bash
runcmd:
  - sed -i -e '/^Port/s/^.*$/Port 2278/' /etc/ssh/sshd_config
  - sed -i -e '/^PermitRootLogin/s/^.*$/PermitRootLogin no/' /etc/ssh/sshd_config
  - sed -i -e '$aAllowUsers demo' /etc/ssh/sshd_config
  - restart ssh"}

```
## wait another command ends

```
## Create a log file
logfile=$(mktemp)

## Run your command and have it print into the log file
## when it's finsihed.
command1 && echo 1 > $logfile &

## Wait for it. The [ ! -s $logfile ] is true while the file is 
## empty. The -s means "check that the file is NOT empty" so ! -s
## means the opposite, check that the file IS empty. So, since
## the command above will print into the file as soon as it's finished
## this loop will run as long as  the previous command si runnning.
while [ ! -s $logfile ]; do sleep 1; done

## continue
command2
```
## Ha Proxy example Centos 7
```
#cloud-config

# set the locale
locale: en_NZ.UTF-8

# timezone: set the timezone for this instance
timezone: UTC

# recreate server ssh keys
ssh_deletekeys: 1

# add an ssh key the default user created by cloud-init
users:
  - name: centos
    sudo: ALL=(ALL) NOPASSWD:ALL
    ssh-authorized-keys:
      - ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQC/UGP/BfbMgm4CpJkBYQWQoBIJ5aq2gOC3RwxkCRoEc3i9D8th6DwC3ESOMtq+gnpVVNNGrcER/6dkS0hrfPcBeFnGri/8luJ4ePjiuMCpeAC9ZTLLRROnda8EgMgWNmkqCBwsrjOa1Tm7rcZQoyBb34PLyrK45qs6J6D8gbA+AVbtcUWuMT+sv+FOUiJnrQVrTQNBgExYWh38kmspxnDh6z1cDdzkNwTdyyCfMXcNurUMIqFICx9IXaaE6FST7lv3h11W55amumcqRfKUGjKc1eiCBL8+mCS1Bhs5gtk4vJX5S5NkYtm3H+y0WhAZAtDRMSwJlBjlC8vS0aZzsq3UYKMPSGamnwjBUmz+ZwSL90mLJwLU5BYU3JSlX6hP+RtJKX9XBi54G7fsPoQMtrffidxjNXObAxovaiwEjHE2iP4iD4loCuGc5aLg43tv87lBxCqAZhTKX8yjz2QQ/0BoaKJxpYoyyfzhLh4/DiVXpvFgUiQa8EKcvz+F7MngS15RMc/0fkmIHY2pTxCjYuzNGVaeuLF8xB4SJ8pk9Q94X7aJcR5FqX1nwU+LkF04d2eV23m/A8fQt5/F0OMTs45CvcCMkC4MmxomFcCQdP5K4qkB7T5QFUF9i1WprCzeT4K5j8UlIOkN+GySaC8MzKopfoQcHOgq5TU+u2RxHwik5w== deploy

packages:
  - haproxy

# runcmd needs a reboot on CentOS 7
runcmd:
  - chkconfig haproxy on
- service haproxy start

```

