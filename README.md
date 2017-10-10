Role Name
=========

This role sets up the dnsmasq package on Ubuntu or Debian.

### Restrictions & Notes
1. This role has been configured for the global static-ip host.
If you need to abtain your global ip dynamically, you need to modify this role by hand.

2. This role doesn't care about NAT/NAPT settings.
You might need to configure iptables by other role.

### Example Network Diagram

     +----------+        +-------------+        +------+
    -|<your ISP>|-<eth0>-| this homegw |-<eth1>-|switch|-+--<client A>
     +----------+        +-------------+        +--+---+ |
                          host: homegw                   +--<client B>
                          domain: example.org
						  eth0 ip: <global static ip>
						  eth1 ip: 192.168.0.1/24

Requirements
------------

This role is tested on the following platforms.

### Ansible
- Version 2.4

### Distributions
- Ubuntu 16.04
- Debian 9


Role Variables
--------------

### Defaults


Dependencies
------------

N/A

Example Playbook
----------------

    - hosts: servers
      roles:
         - YasuhiroABE.ansible-dnsmasq-homegw

License
-------

Apache License 2.0

Author Information
------------------

[Yasuhiro ABE](http://www.yasundial.org/foaf.xml)
