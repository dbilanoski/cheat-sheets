# Configuring Static IP Addresses On Linux

Different distributions handle the network interfaces configuration differently.  For permanenet configuration, we are aiming to change the configuration files.

In case you are running distributions with GUI or multiple network managers, it's best to check which are running and disable those which are not needed.

## Identify And Control Network Managers

To check which ones are running, see below.

```bash
sudo systemctl --type=service | grep --ignore-case 'network'
```

To disable or enable some of them, these can be used.

```bash
sudo systemctl stop NetworkManager
sudo systemctl disable NetworkManager

sudo systemctl start systemd-networkd
sudo systemctl enable systemd-networkd
```

## Red Hat Enterprise Linux Distros

> CentOS, Fedora, Scientific Linux, etc.

On RHEL distributions, configuration is stored under NIC configuration in */etc/sysconfig/network-scripts/ directory.

1.1. Check and take a note of your network interface name.

```bash
ip addr
```

Usually, cable connected interfaces will start with letter "e" and wireless with letter "w".

1.2. Proceed to configure the network interface.

```bash
cd /etc/sysconfig/network-scripts/
ls
# Now find the file called ifcfg-your-interface-name. If there is none, create new.
# Say my name was eth0
sudo nano /etc/sysconfig/network-scripts/ifcfg-eth0 
```

1.3. Add fields below appropriately and save the configuration.

```
DEVICE=eth0
BOOTPROTO=none
ONBOOT=yes
IPADDR=192.168.1.10
NETMASK=255.255.255.0
GATEWAY=192.168.1.254
BROADCAST=192.168.1.255
DNS1=1.1.1.1
DNS1=1.0.0.1
USERCTL=no
NM_CONTROLLED=no
```

Where the options mean:

-   _DEVICE=eth0_ ensures we have the correct interface name
-   _BOOTPROTO_ is _dhcp_ for DHCP, but our static configuration doesn’t require any protocol, so we use _none_
-   _ONBOOT=yes_, similar to Debian’s _auto_, indicates the interface should be activated on boot
-   _IPADDR_, _NETMASK_, _GATEWAY_, and _BROADCAST_ are all set to their respective values for our static address
-   _DNS1_ and _DNS2_ optionally set DNS servers
-   _USERCTL=no_ prevents non-superusers to control the interface
-   _NM_CONTROLLED=no_ stops _NetworkManager_ from managing the interface

1.4. Restart the network services.

```bash
sudo systemctl restart network
```

## Debian Distros

> Kali, Knoppix, Ubuntu, Raspberry Pi OS, etc.

Network configuration scripts on Debian based distributions are stored in */etc/network* directory.

1.1. Check and take a note of your network interface name.

1.2. Edit the */etc/network/interfaces file* where we need to **add a block of code** for the static configuration of specific interface and **remove any other configuration** related to that specific interface.

```shell
sudo nano /etc/network/interfaces
```

```
auto eth1
iface eth0 inet static
  address 192.168.1.10
  netmask 255.255.255.0
  gateway 192.168.1.254
  broadcast 192.168.1.255
  dns-nameservers 1.1.1.1, 1.0.0.1
  
```

Where the options mean:

- auto eth1 enables automatic configuration for this interface during boot
- iface eth1 inet static sets eth1 as an IPv4 interface with a static address
- address, netmask, and gateway assign the respective addresses and network
- dns-nameservers, while not strictly necessary, sets the DNS servers to use

1.3. Restart the system.


## Ubuntu Server

On Ubuntu, [netplan](https://manpages.ubuntu.com/manpages/jammy/man8/netplan-apply.8.html) configuration files are used to configure network interfaces. [Here](https://netplan.io/examples) is a great collection of netplan configuration examples.

1.1. Check and take a note of your network interface name

1.2. Navigate to /etc/netplan/ and either edit the existing configuration file or create 99_config.yaml configuration file as shown below.

```bash
sudo nano /etc/netplan/99_config.yaml
```

```
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      addresses:
        - 192.168.1.10/24
      routes:
        - to: default
          via: 192.168.1.254
      nameservers:
          search: [mydomain, otherdomain]
          addresses: [1.1.1.1, 1.0.0.1]
```

1.3. Apply the new configuration

```bash
sudo netplan apply
```


## References

1. [Good read about manual network configuration in Linux on baeldung.com](https://www.baeldung.com/linux/set-static-ip-address)
2. [Ubuntu official documentation about network configuration](https://ubuntu.com/server/docs/network-configuration)
3. [Netplan official documentation with examples](https://netplan.io/reference)