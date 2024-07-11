---
alias: ad, "active directory"
---
# Integrating Linux to Active Directory

Ubuntu server will be used as an example but the procedure can be applied to other distros. In this approach, we are counting to use samba shares with AD authentication which is why some additional packages are installed and configured.

## Prerequisites

1. AD account that has rights to join a system to the domain
2. A Linux server properly configured with DNS pointers which can find the Domain Controller
3. Accessible Domain Controller

## Procedure

### 1. Basic configuration of the Linux server

1. Configure FQDN host name
   
	```bash
	sudo hostnamectl set-hostname ExampleName
	```
	
	Set alias in hosts file
	```bash
	sudo nano /etc/hosts
	```
	```text
	# Before
	
	127.0.1.1   ExampleName

	# After

	127.0.1.1  ExampleName.your.domain ExampleName
	```
	
2.  Configure [[setting-static-ip-address | static ip address]] with appropriate DNS server for your domain

### 2. Install needed application packages

In this approach, we are using [realmd](https://www.freedesktop.org/software/realmd/) for discovering and interacting with the AD and [chrony](https://chrony.tuxfamily.org/) for synchronizing time against the domain NTP server and [samba](https://www.samba.org/) for configuring shared resources later.

```bash
sudo apt install realmd samba libpam-winbind krb5-user krb5-config chrony
```

**Note** - *windbind* is used instead of the *sssd* for the name resolution of AD objects since Samba network shares require winbind as of version 4.8.

### 3. Configure time synchronization

Check the [[chrony]] procedure.

### 4. Join to Active Directory domain

For this example, we'll use:
- Domain name: example.local
- AD user with admin rights: user01

1. First check if the domain is reachable.

	```bash
	sudo realm discover example.local
	```
	
	If yes, expected result might look something like this.
	
	```text
	example.local
	  type: kerberos
	  realm-name: EXAMPLE.LOCAL
	  domain-name: example.local
	  configured: kerberos-member
	  server-software: active-directory
	  client-software: winbind
	  required-package: libnss-winbind
	  required-package: winbind
	  required-package: libpam-winbind
	  required-package: samba-common-bin
	  login-formats: EXAMPLE\%U
	  login-policy: allow-any-login
	```

2. Join to domain
   
	```bash
	sudo realm join -v --membership-software=samba --client-software=winbind --user=user01 example.local
	```
	
	Where arguments are:
	- membership-softwre - the software to use when joining to the realm
	- client-software - only join realms for which we can use the given client software
	- user - ad user with permisions to join system to the domain
	
	**Example where destionation OU is specified:**
	
	```bash
	sudo realm join -v --membership-software=samba --client-software=winbind --user=user01 --computer-ou="OU=Linux Servers,OU=Servers,DC=example,DC=local" example.local
	```
	
	[realmd manual pages](https://freedesktop.org/software/realmd/docs/realm.html)


### 5. Update the Name Service Switch configuration file

This file needs to be updated so the winbind is also referenced.

```bash
sudo nano /etc/nsswitch.conf
```

Locate this part and add **winbind** to the end of *passwd* and *group* if not already there.

```bash
# Example configuration of GNU Name Service Switch functionality.
# If you have the `glibc-doc-reference' and `info' packages installed, try:
# `info libc "Name Service Switch"' for information about this file.

passwd:         files systemd winbind
group:          files systemd winbind
shadow:         files
gshadow:        files
```


### 6. Test by querying some AD user

```bash
getent passwd EXAMPLE.LOCAL\\someADuser
```

[getent manual pages](https://www.unixtutorial.org/commands/getent)

### 7. SSH access and home folders for AD users

If we expect AD users to SSH into the system, edit the sshd_config file.
   
   ```bash
   sudo nano /etc/ssh/sshd_config
   ```
   
Locate and configure *PasswordAuthentication yes*.

```bash
# To disable tunneled clear text passwords, change to no here!
PasswordAuthentication yes
#PermitEmptyPasswords no
```
   
To enable home folder creation for console/ssh access per user, execute:

```bash
sudo pam-auth-update --enable mkhomedir
```


### Configure Samba share

If those are to be used with Active Directory objects, check the [[samba|samba configuration]] guide.

## References

1. [Ubuntu Official Docs on SSSD and Active Directory](https://ubuntu.com/server/docs/service-sssd-ad)
2. [Ubuntu Official Docs on Samba and Active Directory](https://ubuntu.com/server/docs/samba-active-directory)
3. [Samba Official on Setting up Samba as a Domain Menber](https://wiki.samba.org/index.php/Setting_up_Samba_as_a_Domain_Member)
4. [Debian Official Docs on SSSD and Active Directory](https://wiki.debian.org/AuthenticatingLinuxWithActiveDirectorySssd)
5. [Samba Official about Winbind](https://www.samba.org/~ab/output/htmldocs/Samba3-HOWTO/winbind.html)
6. [Useful post about Guest Access issues on Samba integrated to AD](https://serverfault.com/questions/984385/guest-share-for-domain-integrated-samba-server)
7. [Useful post about getent not resolving AD users depending on the used backend](https://forums.freebsd.org/threads/samba-ad-getent-passwd-doesnt-return-domain-users.62554/)
8. [Useful post about troublehsooting getent not resolving AD users and resetting SAMBA config](https://www.claudiokuenzler.com/blog/1066/samba-getent-passwd-no-active-directory-users-wbinfo-works)







