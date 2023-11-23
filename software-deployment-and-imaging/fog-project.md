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

### Transfering single/multiple images to new server

1. In the FOG web GUI, locate the image you are going to copy to a new server and document/screenshot the image settings
2. Copy image you want from old to new server
	1. Many ways to copy those, example with [[scp|SCP]] below
	   
	```bash
	scp -r myUser@10.191.8.60:/images/HP600G3win10 /images/
	```
	   
3. On the new server, in FOG web GUI, create new image and make sure that the *name* and the *path* are according to the copied image. If you did not change anything, screenshot from the old server can be used letter by letter.

### Transfering all images to new server

1. Delete images you do not need (both in /images folder and via FOG web page GUI)
2. Export image list from FOG web page GUI (Images > export) and save it as a .csv file
3. Copy images from current to new server
	1. Many ways to copy those, example with [[scp|SCP]] below
	   
	```bash
	scp -r myUser@10.191.8.60:/images/\{HP600G3win10,HP705G5_win10,HP600G5-SFF_win10-v2004,HP600G5-DM_win10-v2004_2,HP800G2-win10-v2004,HP600G4_win10\} /images/
	```

SCP example explanation:
* Command initiated on new server (where I want to copy files to)
* Command is saying this: access my old FOG server on 10.191.8.60 as user "myUser" and from /images copy folders HP600G3win10,HP705G5_win10,HP600G5-SFF_win10-v2004,HP600G5-DM_win10-v2004_2,HP800G2-win10-v2004,HP600G4_win10 to the "/images" folder on my new server

Useful SCP examples [here](http://www.hypexr.org/linux_scp_help.php](http://www.hypexr.org/linux_scp_help.php).

4. On your new FOG server, import .csv file with images

### Archiving images

Here we are pulling FOG images from the remote FOG server to our local repository and archiving them on the fly to [tarballs](https://computing.help.inf.ed.ac.uk/FAQ/whats-tarball-or-how-do-i-unpack-or-create-tgz-or-targz-file). Later, they can be transferred to other servers and extracted also on the fly, which makes this approach both simple and practical.


1.  Make sure you have enough space on your repository workstation
   
2.  Make sure you document each image settings, best with screenshot from the FOG web GUI on the server you are getting image from
    
3.  Open CMD as administrator / bash with sudo then run this command:  
      
    ```bash
    Ssh your-username@fog-server-ip-address tar czvf - /images/image-folder-name > C:\desired-folder-on-your-local-machine\image-folder-name.tar.gz  
    ```
    
    Example:  
	```bash
    ssh dbilanoski@10.191.8.60 tar czvf - /images/HP600G3-SFF_win10-v21H2 > C:\fog_images\HP600G3-SFF_win10-v21H2.tar.gz  
    ```
      
    This one will copy, archive and compress image from FOG server at 10.191.8.60 in /images/HP600G3-SFF_win10-v21H2 to C:\\fog_images\\HP600G3-SFF_win10-v21H2.tar.gz  
      
4.  Wait for it to finish - as images are around 10 GB compressed, this might take some time depending on the network configuration
    

Repeat for each image you want to archive  and make sure to document each image settings as suggested in step 2.

### Importing archived image

Here we are copying and unarchiving tar balled images back to the FOG server.

1. Open CMD as administrator on your machine where the archived image is and execute this one:
   
   ```cmd
    Ssh your-username@fog-server-ip-address "cd /images/ && tar -xzv --strip-components 1" < C:\your-downlaoded-image-path\image.tar.gz
   ```
   
   Example:
   ```cmd
   ssh dbilanoski@10.191.8.60 "cd /images && tar -xvz --strip-components 1" < C:\fog_images\HPEliteBook-650-G10_win11-v22H2.tar.gz
   ```
   
   This will also take some time, depending on the local conditions.
	
2. Once transfer is completed and confirmed to be in the /images folder, open FOG GUI, navigate to "Images", create new image and write parameters to match the original when the image was archived. Previous steps there suggested screenshots which is the best method to avoid mistakes. Name and image path are important.

## Issues & Troubleshooting

### Installation script not working with PHP related errors

This is due to outdated PHP version references in the installation script on the "Stable version" brach on [FOG Project Github](https://github.com/FOGProject/fogproject).

#### Solution

Until updated, easiest fix is to use the installation script on the [development version](https://github.com/FOGProject/fogproject#install-latest-development-version) branch where this is updated.

### Boot issues with DHCP timeouts on some Realtek cards

On some newer Realtek NICs issue with DHCP timeout was detected when trying to load ipxe.efi.

#### Solution

1. First update the FOG kernel, see [here](https://wiki.fogproject.org/wiki/index.php?title=Kernel_Update) for instructions, [here](https://docs.fogproject.org/en/latest/reference/manual_kernel_upgrade.html) also
    
	- To check the current version, log to the FOG machine, navigate to /var/www/html/fog/service/ipxe
	- Then type file bzImage - version will be shown
	- Update to latest stable version, update both 32 and 64 images
    

2. If that does not help, switching boot image is needed
    
	- Check on the FOG machine that you have snp.efi and snponly.efi file in the /tftpboot directory
	- On your DHCP server, change scope option 067 from *ipxe.efi* to *snp.efi*
	- Test if this is now working on other production machines
	- Snponly.efi can also be used here, you can check differences [here](https://ipxe.org/appnote/buildtargets)**

To preserve usage of ipxe.efi on cards where snponly.efi is working, you can try this:
    
- Check if there is " DMA Protection" in BIOS - disable it, try to boot
- Check if there is "Mac-address pass-through" in BIOS - disable it, try to boot

#### References

1. https://ipxe.org/err/040ee1
2. https://forums.fogproject.org/topic/15169/pxe-boot-issue-with-hp-probook-450-g8-realtek-nic/14
3. [https://github.com/warewulf/warewulf3/issues/84](https://github.com/warewulf/warewulf3/issues/84)


### Scheduled capture & deploy FOG tasks not initiating on boot

This was observed on newer HP laptops where scheduled capturing would not start on a network boot.

Rather, user would be greeted with the usual FOG network boot screen and message that the HOST is not registered, even though the host is visible in the FOG database. Further more, deployment of image would be possible from the FOG boot menu but not as a scheduled task to be executed prior to the FOG boot menu.

* Re-registering the host will return that the host is already registered.

* Boot image where issue was confirmed: ipxe.efi, smp.efi

#### Solution

This was caused by new "Mac Address passthrough" feature which needs to be disabled or configured manually with custom MAC address.

1. Check BIOS settings
2. Under “Advanced”, locate “MAC Address passthrough”
3. Set it to “Disabled” (or manualy configure custom MAC address)

In case of network warnings during boot where the message shortly says “Legacy IP address wrapper is used” and some hexadecimal value, using smp.efi will fix it.


## References

1.  Boot types in ipxe explained [here](https://ipxe.org/appnote/buildtargets)
2.  Manual upgrade of FOG kernel [here](https://docs.fogproject.org/en/latest/reference/manual_kernel_upgrade.html)
3. Tar & GZip examples [here](https://www.cs.hmc.edu/~mjeffryes/targzip.html)
4. FOG Snapin Packs documentation [here](https://wiki.fogproject.org/wiki/index.php?title=SnapinPacks)


### Not booting to FOG menu on newer HP devices

It was observed on newer HP devices that the network boot to the FOG menu will not work after installing Windows 11, even though scheduled tasks of capturing the image did work while the system was not installed so the capturing was actually possible on the same device.
#### Solution

In the BIOS, under "Advanced", turn off:
* HP Sure Start
* HP Secure Boot
* MAC Address Passthrough should be left to "System" if the device has an ethernet port