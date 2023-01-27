# Managing Users With Linux CLI

## Creating users

To create user with automatic home folder creation and user details configuration, use `adduser` .

```bash
sudo adduser username
```

You will be promted for password and some descriptional parameters.

Using `useradd` will require additional cli arguments for same task.

## Adding users to sudo group

### By Editing The sudoers File

By adding user to the /etc/sudoers file, sudo access can be granted.

```bash
sudo nano /etc/sudoers
```

Find the root user and write same configuration for the user you are granting access to.

```bash
# On example of user dbilanoski
dbilanoski ALL=(ALL:ALL) ALL
```


### On Debian Distributions

By adding user to the *sudo* group (example with user dbilanoski).

```bash
sudo usermod -aG sudo dbilanoski
```


### On RHEL Distributions

By adding the user to the *wheel* group (example with user dbilanoski)

```bash
sudo usermod -aG wheel dbilanoski
```
