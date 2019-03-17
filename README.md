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
 
