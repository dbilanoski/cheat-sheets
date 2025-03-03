# Git Notes

## Git Flow

### Creating a New Repository and Initial Setup


1.  **Create the Repository:**

    * Create a new repository on your chosen hosting service (Bitbucket, GitHub, etc.).
    * Choose a descriptive repository name.

2.  **Clone the Repository:**

    * Clone the newly created repository to your local machine:

        ```powershell
        git clone <repository_url>
        ```

    * Replace `<repository_url>` with the URL provided by your hosting service.

3.  **Initial Setup and File Creation:**

    * Navigate to the cloned repository directory:

        ```powershell
        cd <repository_name>
        ```

    * Create the necessary files and directories for your project.
    * Create a `README.md` file for project description.
    * Create a `.gitignore` file to add files and filetypes that should not be tracked by git.
	    * Good resource for auto creating Git ignore files [here](https://www.toptal.com/developers/gitignore)

4.  **Stage Initial Changes:**

    * Stage all initial files:

        ```powershell
        git add .
        ```

5.  **Commit Initial Changes:**

    * Commit the initial setup with a descriptive message:

        ```powershell
        git commit -m "feat: Initial commit - project setup."
        ```

6.  **Push to Remote Repository:**

    * Push the initial commit to the remote repository:

        ```powershell
        git push origin main # or git push origin master
        ```


7.  **Continue Development:**

    * Follow the branching and pull request workflow for subsequent changes.

### Updating an Existing Repository

Work is performed on a new, separate branch. Once committed to this branch, a pull request is created for review and approval, after which the new branch can be merged into the `main` branch.

1.  **Make sure your local `main` or `master` branch is up-to-date:**

    ```powershell
    git checkout main # or git checkout master
    git pull origin main # or git pull origin master
    ```

2.  **Create and switch to a new branch, following the naming convention:**

    ```powershell
    git checkout -b feature/your-feature-name
    ```

    * `feature/your-feature-name` should be descriptive and reflect the changes being made.

3.  **Make your code changes.**

4.  **Update the `.gitignore` file to exclude unnecessary files or directories.**

5.  **Stage your changes:**

    ```powershell
    # Stage all changes in the current directory and subdirectories.
    git add .
    # Or if you want to target specific files
    git add path/to/your-file1.extension path/to/your-file2.extension 
    ```

6.  **Commit your changes, following the commit message convention:**

    ```powershell
    git commit -m "feat: Clear and concise commit message describing the changes."
    ```

7.  **Push your branch to the remote repository (Bitbucket, etc.):**

    ```powershell
    # For the first push, to create the branch on remote repository
    git push --set-upstream origin feature/your-feature-name 
    # For subsequent pushes to an existing remote branch.
    git push origin feature/your-feature-name 
    ```

8.  **Create a Pull Request and submit it for review.**

9.  **Once the review is complete and approved, merge the Pull Request.**

10. **Clean up locally:**

    ```powershell
    # Switch to the main branch.
    git checkout main
    # Pull the updated main branch.
    git pull origin main 
    # Delete the merged branch.
    git branch -d feature/your-feature-name 
    ```

## Conventions & Standards
###  Commit Message Conventions


**Format:**

`action: RITM/CHG-request_number - commit description`

* **action:** Indicates the type of change.
* **RITM/CHG-request_number:** Refers to the relevant request or change number from your issue tracking system.
* **commit description:** Short but descriptive explanation of the changes made.

**Actions:**

* **feat:** Introduces a new feature.
* **fix:** Corrects a bug or issue.
* **chore:** Cosmetic changes not affecting the code logic.
* **refactor:** Code changes that neither fix a bug nor add a feature.
* **docs:** Documentation-only changes.

**Examples:**

* `feat: RITM-1234 - Add user authentication functionality.`
* `fix: CHG-5678 - Resolve issue with incorrect data display.`
* `chore: RITM-9012 - Update dependencies to latest versions.`
* `refactor: CHG-3456 - Improve code readability in user service.`
* `docs: RITM-7890 - Update README with installation instructions.`

**Guidelines:**

* Keep commit messages concise and to the point.
* Use the imperative mood (e.g., "Add," not "Added").
* Provide sufficient context to understand the changes.
* Reference the relevant request or change number.
* Always add a space after the ":" and before the request number.
* Keep the length of the first line under 72 characters (general standard, visibility without wrapping).

## How To's
### Caching GitHub's Personal Access Tokens

#### Cross-platform

Microsoft provides a cross-platform credential helper named [GCM (Git Credential Manager)](https://github.com/GitCredentialManager/git-credential-manager)
##### Installation & Configuration (Linux, Debian based)
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
##### Uninstall

```shell
git-credential-manager unconfigure
sudo dpkg -r gcm
```


#### Linux 

##### Creating A .netrc File (Not Safe)

Super simple but keeps credentials stored in plain text using a [.netrc file](https://www.gnu.org/software/inetutils/manual/html_node/The-_002enetrc-file.html).

```bash
touch ~/.netrc
echo machine github.com login <login-id> password <token-password> > ~/.netrc
```


### Delete Commit History But Keep Changes In A Repo

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


### Change Remote URL In Local Repository

Open terminal, navigate to your repository folder.

```powershell
# To see current remote URL
git remote -v

# To update it
git remote set-url origin https://your-url.git
```
