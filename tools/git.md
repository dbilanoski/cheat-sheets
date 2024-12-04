# Git Notes

## Caching GitHub's Personal Access Tokens

### Cross-platform

Microsoft provides a cross-platform credential helper named [GCM (Git Credential Manager)](https://github.com/GitCredentialManager/git-credential-manager)
#### Installation & Configuration (Linux, Debian based)
Download the latest [.deb package](https://github.com/git-ecosystem/git-credential-manager/releases/latest) and run the following:

```shell
sudo dpkg -i <path-to-package>
git-credential-manager configure
```

Then execute this to configure default credential store to be used:

```bash
git config --global credential.credentialStore gpg
```

Now install [pass](https://www.passwordstore.org/) as it will be needed to initialize the store:

```bash
sudo apg install pass
```

Create a new gpg key pair as it will be also needed during initialization:

```bash
gpg --gen-key
```

Initialize the store with pass and newly created key:

```bash
pass init <gpg-id>
```
#### Uninstall

```shell
git-credential-manager unconfigure
sudo dpkg -r gcm
```


### Linux 

#### Creating A .netrc File (Not Safe)

Super simple but keeps credentials stored in plain text using a [.netrc file](https://www.gnu.org/software/inetutils/manual/html_node/The-_002enetrc-file.html).

```bash
touch ~/.netrc
echo machine github.com login <login-id> password <token-password> > ~/.netrc
```


## Delete Commit History But Keep Changes In A Repo

Easiest approach is to create new branch,  add everything to it, delete the old branch and then rename the new one to match the old one.

1. Create new orphan branch
	* An orphan branch is a separate branch that starts with a different root commit. So the first commit in this branch will be the root of this branch without having any history. It can be accomplished by using the Git checkout command with the --orphan option.

```powershell
git checkout --orphan ini-branch
```

2. Add everything to it and make the new first commit
   
```powershell
git add --all
```

```powershell
git commit -m "Initial Commit"
```

3.  Delete the old branch

```powershell
git branch -D master
```

4. Rename new branch to match the name of the old
   
```powershell
git branch -m master
```

5.  Force push to remote repository

```powershell
git push -f origin master
```


## Change Remote URL In Local Repository

Open terminal, navigate to your repository folder.

```powershell
# To see current remote URL
git remote -v

# To update it
git remote set-url origin https://your-url.git
```
