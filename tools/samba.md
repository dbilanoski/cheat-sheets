# Samba Cookbook

## Install Samba

Depending on your distributions, software repository commands might look different. 
Example is on Ubuntu server.

**Install Samba**

```bash
Sudo apt install samba
```

**Start services and enable them on startup**

```bash
sudo systemctl start smbd.service
sudo systemctl start nmbd.service
```
```bash
sudo systemctl enable smbd.service
sudo systemctl enable nmbd.service
```

**Create Samba user**
   
In this example, we are creating smbglobal user.
   
```bash
sudo smbpasswd -a smbglobal
```


## Simple Samba share with unrestricted access


## Samba shares with Active Directory Authentication

First prepare the Linux host and join it to Active Directory as per [[linux-to-AD]] guide.

### Summary

In this setup, we are using:
- Active Directory objects for managing access to the share
- Local user for managing file permissions


### Procedure

#### 1. Prepare local users

Create local user and group which will be owners of the share.

In this example, we are creating user *smbglobal* and group *smbglobals*.

```bash
sudo groupadd --system smbglobals
```
```bash
sudo useradd --system --no-create-home -g smbglobals -s /bin/false smbglobal
```
```bash
sudo passwd smbglobal
```


#### 2. Prepare folders

Create folder which will be root of your share.

In this example, we are creating folder on the root of the file system called *share* and setting owners to be smbglobal/smbglobals.

```bash
$ cd /

$ sudo mkdir /share

$ sudo chown smbglobal /share

$ sudo chgrp smbglobals /share

$ sudo chmod g+w /share
```


#### 3. Configure Samba

Make sure you installed Samba as explained in the [[samba#Install Samba | Install Samba]] chapter.


##### 3.1. Edit configuration file

First backup current config, then start writing new.

```bash
sudo mv /etc/samba/smb.conf /etc/samba/smb.conf.old
```
```bash
sudo nano /etc/samba/smb.conf
```

Here we are saying:
- Access via example.local AD objects
- Shared folder called *share* is read only by default except for objects under *write list*
- Write is permitted for AD members of security  group *Admins*, local group *smbglobals*  and members of security groups *Domain Controllers* and *Domain computers* which will provide access for processes under [local system accounts](https://learn.microsoft.com/en-us/windows/win32/services/localsystem-account).
- Everything created by external users in the *share* directory will be owned by smbglobal/smbglobals

```bash
[global]
	security = ADS
	workgroup = EXAMPLE
	realm = EXAMPLE.LOCAL
	
	log file = /var/log/samba/%m.log
	log level = 2
	
	# Default ID mapping configuration using the autorid
	# idmap backend. This will work out of the box for simple setups
	# as well as complex setups with trusted domains.
	idmap config * : backend = autorid
	idmap config * : range = 10000-9999999
	
	template shell = /bin/bash
	winbind use default domain = no

[share]
	path = /share
	comment = Shared SMB directory with AD authentication
	
	# Read only by default but with explicitly stated write access exceptions
	read only = yes
	valid users = @"EXAMPLE.LOCAL\Domain Controllers", @"EXAMPLE.LOCAL\Domain Computers", @"EXAMPLE.LOCAL\Admins", @"EXAMPLE.LOCAL\unattended_user", @smbglobals, smbglobal
	Write list =  @"EXAMPLE\Domain Controllers", @"EXAMPLE.LOCAL\Domain Computers", @"EXAMPLE.LOCAL\Admins", smbglobal
	
	# Everything created in that folder by external users will be using this parameters
	force user = smbglobal
	force group = smbglobals
	create mask = 0775
	force create mode = 0775
	directory mask = 0775
```

##### 3.2. Test the configuration and reload services

```bash
tesparm
```

If there are no errors, reload Samba services.

```bash
sudo systemctl restart smbd nmbd
```


##### 3.3. Test Access

Test access by visiting "\\\\your-server-ip\share" with your Active Directory accounts.


## Troubleshooting & Issues

### Access Issues with SMB Signing

SMB signing enforcement screws guest access on insecure samba servers - guest account mapping needs to be removed otherwise users accessing from Windows are not prompted to enter credentials even for non-guest share.

In the */etc/samba/smb.conf*, remove `map to guest = bad user`

[Useful forum post about the topic](https://askubuntu.com/questions/1079924/make-samba-on-ubuntu-18-04-work-with-windows-clients-which-require-digitally-sig)


## References

1. [Good read on troubleshooting Samba with AD integration](https://www.claudiokuenzler.com/blog/1066/samba-getent-passwd-no-active-directory-users-wbinfo-works)