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
	dnsmasq_eth_device: "br0"
	* Listen on the specified interface.

	dnsmasq_ipv4_hostprefix: 192.168.0.1/24
	* A static ip address and netmask preffix on the {{ dnsmasq_eth_device }}.

	dnsmasq_ipv4_domainname: example.org
	* A domain name for the internal, home, network.

	dnsmasq_dhcp_domain_search_list:
	  - "{{ dnsmasq_ipv4_domainname }}"
	* For a domain search list on dhcp clients.

	dnsmasq_dhcp_range:
	  from_ipaddr: 192.168.0.200
	  to_ipaddr: 192.168.0.240
	* IP address will be assigned with this from-to range.

	dnsmasq_external_nameservers:
	  - 8.8.8.8
	* This value used for the dnsmasq to resolve DNS request.

	dnsmasq_local_addresses:
	  - { hostname: homegw, ip: 192.168.0.1 }
	* (Expanding to the 'address=<hostname>/<ip>' line)

	dnsmasq_subnet_dnsservers:
	  - { domain: sub1.example.org, server: 192.168.1.53 }
	  - { domain: sub2.example.org, server: 192.168.2.53 }
	* (Expanding to the 'server=<domain>,<server>' line.)

	dnsmasq_dhcp_static_addresses:
	  - { mac: "00:00:00:00:00:00", ip: 192.168.1.10, comment: "example: device name" }
	* (Expanding to the 'dhcp-host=<mac>,<ip>' line with a comment line)

	dnsmasq_sysctl_rules:
	  - { name: net.ipv4.ip_forward, value: 1 }
	  - { name: net.ipv4.conf.default.rp_filter, value: 1 }
	  - { name: net.ipv4.conf.all.rp_filter, value: 1 }
	  - { name: net.ipv4.tcp_syncookies, value: 1 }
	* The /etc/sysctl.conf configuration for dnsmasq.

	dnsmasq_config_filepath:
	  - { template: dnsmasq.homegw.conf.j2, dest_filepath: /etc/dnsmasq.d/homegw.conf }

	dnsmasq_config_permission:
	  owner: root
	  group: root
	  mode: '0644'
	* Even if you specify multiple configuration templates, permission of all transfered files will be same value.

	dnsmasq_ntpd_enabled: False
	* If True, the 'dhcp-optio=option:ntp-server,{{ dnsmasq_ipv4_hostprefix|ipaddr('address') }}' config ill be enabled.

Dependencies
------------

The 'python-netaddr' package needs to be installed on the ansible host controller.

Example Playbook
----------------

	  hosts: all
	  vars:
	    dnsmasq_eth_device: "eth1"
	    dnsmasq_ipv4_hostprefix: 192.168.0.1/24
	    dnsmasq_ipv4_domainname: example.org
	    dnsmasq_dhcp_domain_search_list:
	      - "{{ dnsmasq_ipv4_domainname }}"
	    dnsmasq_dhcp_range:
	      from_ipaddr: 192.168.0.200
	      to_ipaddr: 192.168.0.240
		dnsmasq_ntpd_enabled: True
		## followings are optional
	    dnsmasq_local_addresses:
	      - { hostname: homegw, ip: 192.168.0.1 }
	      - { hostname: sub1gw, domain: sub1.example.org, ip: 192.168.0.1 }
	    dnsmasq_subnet_dnsservers:
	      - { domain: sub1.example.org, server: 192.168.1.53 }
	    dnsmasq_dhcp_static_addresses:
	      - { mac: "00:00:00:00:00:10", ip: 192.168.1.10, comment: "device name #10" }
	      - { mac: "00:00:00:00:00:20", ip: 192.168.1.20 }

License
-------

Apache License 2.0

Author Information
------------------

[Yasuhiro ABE](http://www.yasundial.org/foaf.xml)
