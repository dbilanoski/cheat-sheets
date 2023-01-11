FOG project is a free open-source network computer cloning and management solution.

# Installation

Official documentation [here](https://docs.fogproject.org/en/latest/).

Prior to installing the FOG project, make sure to have the Linux box configured with **static IP address** and other parameters specific to your environment.

Also, if you are using external DHCP service, make sure that scope options 066 and 067 are configured as explained [here](https://docs.fogproject.org/en/latest/installation/network_setup.html)

## Check firewall configuration

If firewall is active, disable it.

### Ubuntu

```bash
ufw status
ufw disable
```

### CentOS

```bash
sudo systemctl status firewalld
sudo systemctl disable firewalld # Disable service starting on boot
sudo systemctl mask --now firewalld # Mask the servise so other programs would not be able to start it
```

Additional, for CentOS, SELinux needs to be configured as permisive.

```bash
sed -i.bak 's/^.*\SELINUX=enforcing\b.*$/SELINUX=permissive/' /etc/selinux/config # This will set it permanently

setenforce 0 # This will do only for the current session, gone after reboot
```

## Download and install the program

FOG Project application can be downloaded from the official GitHub repo [here](https://github.com/FOGProject/fogproject) 

As there is a known issue with the PHP version in the stable branch installation script, at the moment it's best to use the Development version.

1. Download and extract the FOG project
   
```bash
wget https://github.com/FOGProject/fogproject/archive/dev-branch.tar.gz; tar xzf dev-branch.tar.gz
```

2. Navigate to the extracted folder and execute the installation script
   
```bash
cd /path/to/fogproject-dev-branch/bin
sudo ./installfog.sh
```

3. Follow the textual configurator and insert desired values. Instalation options are described [here](https://docs.fogproject.org/en/latest/installation/install_fog_server.html)

| Item | Description |
|------|--------------|
|Storage | ----- |

4. At some point, you will be directed to access the web gui to start the database installation do  that and follow the on-screen instructions. You will be prompted  to configure database password.

On completion, you will be able to access your FOG server via http://your-fog-ip-address/fog/management.


## DHCP Options

FOG can act as a DHCP service. In case external DHCP server will be used, two scope options need to be configured on it for the network boot to be working.

### Option 66 

Also called "Boot server" or "TFTP server", set it to the ip address of the FOG server.

### Option 67

Also called "Bootfile Name" is the name of the boot image to be loaded during network boot.

For UEFI clients, set it to *ipxe.efi*
For legacy clients, set it to *undionly.kpxe*


# Capturing images

# Deploying images

# Transfering and archiving images


