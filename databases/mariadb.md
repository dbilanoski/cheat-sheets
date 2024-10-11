# MariaDB Notes

## Allow Remote Access

By default, MariaDB only allows connections from `localhost`.

On Ubuntu example, here are the steps to allow it.

### 1. Edit The MariaDB Configuration File

Open config file in text editor.
```shell
sudo nano /etc/mysql/mariadb.conf.d/50-server.cnf
```

Find the line restricting MariaDB to 'localhost':
`bind-address = 127.0.0.1`

and change it to:
`bind-address = 0.0.0.0`

Alternatively, you can specify a specific IP address if you want MariaDB to listen on a particular interface.

Save the changes in the file and restart `mariadb` service:

```shell
sudo systemctl restart mariadb
```

### 2. Allow MariaDB User To Have Remote Access

By default, MariaDB users are only allowed to connect from `localhost`.

Log in to the MariaDB server as the root user:

```shell
sudo mysql -u root -p
```

You can create a new user or modify an existing user to allow access from a remote machine.

For an existing user:
* To allow access from **any** IP address:
  ```sql
	  GRANT ALL PRIVILEGES ON *.* TO 'username'@'%' IDENTIFIED BY 'password';
	```
* To allow access from a **specific** IP address (e.g., `192.168.1.100`):
    ```sql
	  GRANT ALL PRIVILEGES ON *.* TO 'username'@'192.168.1.100' IDENTIFIED BY 'password';
	```
After running the appropriate command, flush the privileges to apply the changes:
```sql
FLUSH PRIVILEGES;
```

### Configure Ubuntu Firewall
If you are using the `ufw` firewall on Ubuntu, you need to allow traffic on the default MariaDB port (`3306`).
* To allow access from a specific IP (e.g., `192.168.1.100`):
  ```shell
  sudo ufw allow from 192.168.1.100 to any port 3306
	```
* To allow access from **any** IP address (which can be a security risk):
    ```shell
  sudo ufw allow 3306
	```