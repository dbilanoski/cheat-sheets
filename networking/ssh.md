# SSH Notes

SSH (Secure Shell Protocol) Is a [cryptographic](https://en.wikipedia.org/wiki/Cryptography "Cryptography") [network protocol](https://en.wikipedia.org/wiki/Network_protocol "Network protocol") for operating [network services](https://en.wikipedia.org/wiki/Network_service "Network service") securely and remotely over an unsecured network mostly used for remote administration, file transfer and secure login and command-line execution on a remote computer.

## Connecting Remotely

```shell
ssh -l <your-username> <remote-server-hostname-or-ip-address>
```

Command line options available [here](https://www.ssh.com/academy/ssh/command).

On the first connection, you'll be asked to confirm connection which will add the server to the list of known hosts located at `~/.ssh/known_hosts`.

## Creating SSH Key Pairs For Authentication

To access remote resource securely using SSH, generate an SSH key pair, associate the public key with your remote resource and use the private key to log in to the resource.

### Unix / Linux Systems

#### Creating An SSH Key Pair

1. By using the [OpenSSH](https://en.wikipedia.org/wiki/OpenSSH) authentication utility [ssh-keygen](https://man.openbsd.org/ssh-keygen.1), execute:

	```shell
	ssh-keygen -t ed25519 -C "some comment, such as email or user id if needed"
	```

	Where:
	* `-t` is type of the key to be created (default algorithm Ed25519).
	* `-C` is a free text comment some services require, such as GitHub asking your email address there.

	Other common options might include:
	* `-b` for specifying length in bits (2048 for example).

2. You will be asked for a path, best to keep defaults (/home/current-user/.ssh/).

	```shell
	Enter a file in which to save the key (/home/YOU/.ssh/ALGORITHM):[Press enter]
	```

3. Then for a passphrase:
   
	```shell
	Enter passphrase (empty for no passphrase): [Type a passphrase]
	Enter same passphrase again: [Type passphrase again]
	```

This will create SSH key pair consisting of a public key and a private key. The file name of the public key is created automatically by appending `.pub` to the name of the private key file. For example, if the file name of the SSH private key is `id_ed25519`, the file name of the public key would be `id_ed25519.pub`.

#### Adding Keys To ssh-agent

The [ssh-agent](https://www.ssh.com/academy/ssh/agent) is a helper program that keeps track of users keys and passphrases and it can use the keys to log in to other computers without having the user type a passphrase again (kind of a single-sign-on).

1. Check if ssh-agent is running  (run it in case it's not running)
   ```shell
   eval "$(ssh-agent -s)"
   ```

2. Add keys to the agent
   ```shell
   ssh-add ~/.ssh/nameofkey
	```

To list added keys, do:

```shell
ssh-add -l
```

Now configure remote resource with your public key so you can access it securely.


## References

1. [Official SSH Pages - Usage, Options and Configuration](https://www.ssh.com/academy/ssh/command)
