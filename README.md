LVM Ansible role
================

Role to manage LVM Groups and Volumes. Can be used to create, extend or resize LVM Groups and volumes. To avoid managing
devices list on inventories, creation of VG is now separate from managing (resizing, mounting LV, etc.)

Requirements
------------

No more need for devices/disks to be part of the LVM setup. Now use `lvm_action` instead.

Role Variables
--------------

`lvm__install` For install only (default: False)
`lvm__configure` For configuration  (default: False)
`lvm__vmgroup` is the default system group for VM disks
`lvm__groups` is a list containing vgs (see vgs bellow).
`lvm__action` is used for VG creation/upgrade

### vgs

- `vgname`: uniq name
- `disks`: add disks/partitions to vg (comma separated)
- `state`: present (-> create), absent (-> remove)
- `lvnames`: lv list (see bellow)

### lvnames

- `lvname`: uniq name
- `size`: define size of lvol (ex: "10G", "512M"...)
- `state`: defines if lvol should exist, mounted, exported or be removed...Values are mounted,active,inactive,absent
- `filesystem`: defines filesystem to format lvol as
- `mount_point`: defines mountpoint
- `mount_options`: defines mount options (comma separated)

Dependencies
------------

None

Example Playbooks
-----------------

Create a new VG named `new_vg`.

```
    - hosts: servers
      vars:
        lvm_action: "add_vg"
        lvm_vgname: "new_vg"
      roles:
         - lvm
```

Apply configuration with multiple LV on the VG named `misc-vg`

```
    - hosts: servers
      vars:
        lvm__configure: True
        lvm_groups:
          - vgname: misc-vg
            state: present
            lvnames:
              - lvname: swap_1
                size: 5g
                state: mounted
                filesystem:
              - lvname: mysql
                size: 40g
                state: inactive
                filesystem: ext4
                mount_point: /var/lib/mysql
                mount_options: 'defaults,noatime'
          # VG whitout LV
          - vgname: test-vg
            state: present
            lvnames: []

      roles:
         - lvm
```

License
-------

GPL v3

Author Information
------------------

