Role Name
=========

Installs GlusterFS http://www.gluster.org/ (configures and creates cluster)

Requirements
------------

Add hard drive devices to server nodes to configure for LVM bricks and set glusterfs_config_lvm: true

Role Variables
--------------

````
---
# defaults file for ansible-glusterfs
config_glusterfs: false  #defines if glusterfs should be configured
config_hosts: false  #defines if /etc/hosts should be updated with nodes
glusterfs_brick_dir: /mnt/gluster  #defines the mountpoint for gluster bricks and volumes to be created
glusterfs_client_group: glusterfs-client-nodes  #defines the Ansible inventory group for clients
glusterfs_client_mounts:
  - mountpoint: /mnt/scripts
    src: '{{ glusterfs_primary_server }}:/scripts'
    fstype: 'glusterfs'
    opts: 'defaults,_netdev,backupvolfile-server={{ glusterfs_secondary_server }}'
    state: mounted
  - mountpoint: /mnt/webs
    src: '{{ glusterfs_primary_server }}:/webs'
    fstype: 'glusterfs'
    opts: 'defaults,_netdev,backupvolfile-server={{ glusterfs_secondary_server }}'
    state: mounted
glusterfs_config_lvm: false  #defines if lvm should be configured
glusterfs_create_bricks:  #defines glusterfs bricks to create
  - name: scripts
    owner: root
    group: root
  - name: webs
    owner: root
    group: root
glusterfs_debian_repo_info:  #defines Debian repo for GlusterFS
  keyserver: 'keyserver.ubuntu.com'
  id: 'F7C73FCC930AC9F83B387A5613E01B7B3FE869A9'
  repo: 'deb http://ppa.launchpad.net/gluster/glusterfs-{{ glusterfs_version }}/{{ ansible_distribution|lower }} {{ ansible_distribution_release|lower }} main'
glusterfs_lvm_vols:  #defines GlusterFS LVM volumes to create (Used for GlusterFS Bricks)
  - lvname: glusterfs-lv
    disks:
      - /dev/sdb
      - /dev/sdc
    filesystem: xfs
    lvsize: 100%FREE
    mountpoint: '{{ glusterfs_brick_dir }}'
    vgname: glusterfs-vg
glusterfs_nodes_group: glusterfs-nodes  #defines the Ansible inventory group name for all nodes part of GlusterFS
glusterfs_primary_server: '{{ glusterfs_server_master }}'  #defines primary server for GlusterFS clients to connect to
glusterfs_secondary_server: node1  #defines secondary server for GlusterFS clients to connect to
glusterfs_repl_int_address: ansible_eth1.ipv4.address  #defines interface to configure for glusterfs replication...define here or in group_vars/group
glusterfs_server: false  #defines if host is a glusterfs server
glusterfs_server_group: glusterfs-server-nodes  #defines the hosts inventory group to configure...define here or in group_vars/group
glusterfs_server_master: node0  #defines the node to be considered as GlusterFS master
glusterfs_version: 3.6
````

Dependencies
------------

None

Example Playbook
----------------

````
- hosts: all
  become: true
  vars:
    - config_glusterfs: true
    - config_hosts: true
    - glusterfs_config_lvm: true
    - pri_domain_name: 'test.vagrant.local'
  roles:
    - role: ansible-glusterfs
  tasks:
````

License
-------

BSD

Author Information
------------------

Larry Smith Jr.
- @mrlesmithjr
- http://everythingshouldbevirtual.com
- mrlesmithjr [at] gmail.com
