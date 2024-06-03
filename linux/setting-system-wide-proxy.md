# Configuring Proxy Address On Linux

There are various solutions for this depending on the distro - keeping this note open to add them later, below are on Ubuntu.

## HTTP Proxy With Environment Variables

These variables can be used to configure proxy settings and most applications will pick them automatically.

HTTP Proxy
```ini
proxy_http=username:password@proxy-server-ip:port
```

HTTPS Proxy
```ini
proxy_https=username:password@proxy-server-ip:port
```

FTP Proxy
```ini
proxy_ftp=username:password@proxy-server-ip:port
```

To disregard the proxy settings for local traffic

```shell
export NO_PROXY=localhost,127.0.0.1,::1
```

**Notes**
* Configuration can be temporary or permanent and can be session-wide (user level) or system-wide (system level).
* `username:password` is optional.

### Temporary Proxy Settings

Use these commands in the terminal to configure temporary proxy settings (will disappear after the reboot).

```shell
export HTTP_PROXY=[username]:[password]@[proxy-web-or-IP-address]:[port-number] 
export HTTPS_PROXY=[username]:[password]@[proxy-web-or-IP-address]:[port-number] 
export FTP_PROXY=[username]:[password]@ [proxy-web-or-IP-address]:[port-number] 
export NO_PROXY=localhost,127.0.0.1,::1
```

### Permanent Proxy Settings

#### Session Wide Proxy Settings

These are configured in the user's `~/.bashrc` file.

1. Open `~/.bashrc` with a text editor.
2. Add lines below at the end of the file.
3. Save and exit.

```shell
export HTTP_PROXY="[username]:[password]@[proxy-web-or-IP-address]:[port-number]"
export HTTPS_PROXY="[username]:[password]@[proxy-web-or-IP-address]:[port-number]" 
export FTP_PROXY="[username]:[password]@ [proxy-web-or-IP-address]:[port-number]" 
export NO_PROXY="localhost,127.0.0.1,::1"
```

To validate configuration, check with:
```bash
source ~/.bashrc
```

#### System Wide Proxy Settings

These apply to all users and can be configured in `/etc/profile` or `/etc/environment`.

1. Open `/etc/environment` with a text editor
2. Add lines below (updated with your proxy values)
3. Save and exit, then restart.

```shell
export HTTP_PROXY="[username]:[password]@[proxy-web-or-IP-address]:[port-number]"
export HTTPS_PROXY="[username]:[password]@[proxy-web-or-IP-address]:[port-number]" 
export FTP_PROXY="[username]:[password]@ [proxy-web-or-IP-address]:[port-number]" 
export NO_PROXY="localhost,127.0.0.1,::1"
```


## Proxy Settings For APT

APT can be configured with proxy settings separately from the environment configuration. To configure proxy settings for the APT, create or update `/etc/apt/apt.conf` file.

1. Create\open  `/etc/apt/apt.conf` file.
2. Add lines below (updated with your proxy values).
3. Save and exit.

```shell
Acquire::http::Proxy "http://[username]:[password]@ [proxy-web-or-IP-address]:[port-number]"; 
Acquire::https::Proxy "http://[username]:[password]@ [proxy-web-or-IP-address]:[port-number]";
```

To install a package by specifying the proxy in the terminal, this one can be used:
```shell
'http://username:password@proxy-server-ip:8080' apt-get install package-name
```


## Refernces

1. [Good read about configuring Ubuntu proxy](https://operavps.com/docs/setup-proxy-on-ubuntu/)
2. [Ubuntu Environment Variables Official Docs](https://help.ubuntu.com/community/EnvironmentVariables)
3. [Superuser discussion about different places where environment variables are confiugred](https://superuser.com/questions/664169/what-is-the-difference-between-etc-environment-and-etc-profile)


