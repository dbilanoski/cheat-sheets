# Chrony

Implementation of the Network Time Protocol (NTP) used for time syncing on Linux, BSD and MacOS systems.

## Installation

```bash
sudo apt install chrony
```

## Configuration

Access the config file

```bash
sudo nano /etc/chrony/chrony.conf
```

Locate this part and comment out default servers and add your own NTP servers addressess.

```bash
# This will use (up to):
# - 4 sources from ntp.ubuntu.com which some are ipv6 enabled
# - 2 sources from 2.ubuntu.pool.ntp.org which is ipv6 enabled as well
# - 1 source from [01].ubuntu.pool.ntp.org each (ipv4 only atm)
# This means by default, up to 6 dual-stack and up to 2 additional IPv4-only
# sources will be used.
# At the same time it retains some protection against one of the entries being
# down (compare to just using one of the lines). See (LP: #1754358) for the
# discussion.
#
# About using servers from the NTP Pool Project in general see (LP: #104525).
# Approved by Ubuntu Technical Board on 2011-02-08.
# See http://www.pool.ntp.org/join.html for more information.
server YOUR-SERVER1-NAME-OR-IP.YOUR.DOMAIN    iburst
server YOUR-SERVER2-NAME-OR-IP.YOUR.DOMAIN    iburst
#pool ntp.ubuntu.com        iburst maxsources 4
#pool 0.ubuntu.pool.ntp.org iburst maxsources 1
#pool 1.ubuntu.pool.ntp.org iburst maxsources 1
#pool 2.ubuntu.pool.ntp.org iburst maxsources 2
```

Restart the chrony service

```bash
sudo systemctl restart chrony.service
```


## Validation

Check your sources

```bash
chronyc sources -v
```

Check if the time sync is active

```bash
timedatectl status
```


## References

1. [Official pages][https://chrony.tuxfamily.org/]
2. [Red Hat Guides](https://www.redhat.com/sysadmin/chrony-time-services-linux)

