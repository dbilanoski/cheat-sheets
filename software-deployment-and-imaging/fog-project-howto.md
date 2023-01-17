---
alias: fog
---

# FOG Project Notes

FOG project is a free open-source network computer cloning and management solution.

## Installation

Official documentation [here](https://docs.fogproject.org/en/latest/).

Prior to installing the FOG project, make sure to have the Linux box updated and configured with **static IP address** and other parameters specific to your environment.

Also, if you are using external DHCP service, make sure that scope options 066 and 067 are configured as explained [here](https://docs.fogproject.org/en/latest/installation/network_setup.html)

### Disable Firewall

#### Ubuntu

```bash
ufw status
ufw disable
```

#### CentOS

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

### Download and install the program

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
4. At some point, you will be directed to access the web gui to start the database installation do  that and follow the on-screen instructions. You will be prompted  to configure database password.

On completion, you will be able to access your FOG server via http://your-fog-ip-address/fog/management.


### DHCP Options

FOG can act as a DHCP service. In case external DHCP server will be used, two scope options need to be configured on it for the network boot to be working.

#### Option 66 

Also called "Boot server" or "TFTP server", set it to the ip address of the FOG server.

#### Option 67

Also called "Bootfile Name" is the name of the boot image to be loaded during network boot.

For UEFI clients, set it to *ipxe.efi*
For legacy clients, set it to *undionly.kpxe*


## Capturing images

To ensure there are no issues, make sure that the host OS was generalized with [[sysprep-procedures|sysprep]] procedure prior to capturing the image.

In mixed vendor environment, it's best to maintain FOG images per computer model.

### Procedure order

1. Perform host registration to the FOG server
2. Sysprep the host os with the shutdown option
3. Create the image in the FOG web GUI and associate it with previosly registered host
4. Schedule the capturing task in the FOG web GUI on previously registered host
	1. Schedule the task from the "Hosts" menu on the host itself so you get the option to remove "Wake on LAN" as that one will turn on the host automatically.
5. Execute network boot and the capturing will start automatically

Detailed instructions available in the [official documentation](https://docs.fogproject.org/en/latest/getting_started/capture_an_image.html).

## Deploying images

Images can be deployed either as a standalone task  without host registration or as a task being part of a managed FOG host.

Detailed instructions available in the [official documentation](https://docs.fogproject.org/en/latest/getting_started/deploy_an_image.html).


## Transfering and archiving images

### Transfering all images to new server

1. Delete images you do not need (both in /images folder and via FOG web page GUI)
2. Export image list from FOG web page GUI (Images > export) and save it as a .csv file
3. Copy images from current to new server
	1. Many ways to copy those, example with [SCP](https://en.wikipedia.org/wiki/Secure_copy_protocol) below
	   
```bash
scp -r myUser@10.191.8.60:/images/\{HP600G3win10,HP705G5_win10,HP600G5-SFF_win10-v2004,HP600G5-DM_win10-v2004_2,HP800G2-win10-v2004,HP600G4_win10\} /images/
```

SCP example explanation:
* Command initiated on new server (where I want to copy files to)
* Command is saying this: access my old FOG server on 10.191.8.60 as user "myUser" and from /images copy folders HP600G3win10,HP705G5_win10,HP600G5-SFF_win10-v2004,HP600G5-DM_win10-v2004_2,HP800G2-win10-v2004,HP600G4_win10 to the "/images" folder on my new server

Useful SCP examples [here](http://www.hypexr.org/linux_scp_help.php](http://www.hypexr.org/linux_scp_help.php).

4. On your new FOG server, import .csv file with images