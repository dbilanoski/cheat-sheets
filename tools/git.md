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
    git checkout main
    git pull origin main
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

### Reverting to Older Versions

During development or after deployment, it may be necessary to return to a previous stable state. Git provides multiple approaches depending on the situation and whether the changes have been shared with others.

|Scenario|Recommended Approach|
|---|---|
|Undo a specific change safely|`git revert`|
|Inspect or branch from an old version|`git checkout`|
|Reset branch history (local or controlled use)|`git reset`|
|Restore a known release version|Git tags|
#### Reverting a Commit (Safe for Shared Branches)

Use this method when a change has already been pushed and possibly merged into `main` as it:

- Creates a new commit that undoes the specified commit.
- Does **not rewrite history** (safe for teams).

1. List the commit history to find the one you wish to revert to
   
   ```powershell
   git log --oneline
   ```
   
2. Revert to that commit. New commit will be created reversing the changes.
   
   ```powershell
   git revert <commit-hash>
   ```
   
3. Push the reverted commit 

```powershell
git push origin main
```

#### Restore Project To A Previous Commit (Read Only By Default)

Use this to inspect or temporarily work with a previous state.

1. List the commit history to find the one you wish to check out
   
   ```powershell
     git log --oneline
   ```
   
1. Check-out to an earlier commit. This puts the repository in a **detached HEAD** state, meaning it's read only.
   
   ```powershell
   git checkout <commit-hash>
   ```

If you want to continue working from that point:

```powershell
git checkout -b feature/restore-from-old-version
```

#### Resetting to a Previous Commit (Use with Caution)

This method rewrites history and should only be used in controlled scenarios. It:
- Rewrites commit history.
- Can disrupt other team members.
- Use only when working alone or with team agreement.

**Soft Reset (keeps changes staged):**

```powershell
git reset --soft <commit-hash>
```

**Hard Reset (discards all changes):**

```powershell
git reset --hard <commit-hash>
```

If the branch has already been pushed:

```powershell
git push --force
```

#### Using Tags for Versioning (Recommended for Releases)

TODO: Need to test, review this add tagging to the flow so releases have them. Might be good fit for us since almost every change is a release and reverting to previous release might be cleanest approach.

1. List tags
   
   ```powershell
   git tag
   ```
   
2. Check out a tagged version
   
   ```powershell
   git checkout v1.0.0
   ```
   
3. Create a branch from it which can later be deployed
   
   ```powershell
   git checkout -b hotfix/v1.0.0 v1.0.0
   ```

## Conventions & Standards
###  Commit Messages Conventions

**Format:**

`{action}/{request-id}-{short-description}`

* **action:** Indicates the type of change.
* **RITM/CHG-request_number:** Refers to the relevant request or change number from your issue tracking system.
* **commit description:** Short but descriptive explanation of the changes made.

**Actions:**

* **feat:** Introduces a new feature or modifies existing functionality (e.g., changes in business logic).
- **fix:** Resolves a bug or issue affecting functionality.
- **refactor:** Improves existing code structure without changing behavior (e.g., renaming variables, optimizing logic).
- **docs:** Updates documentation, comments, or README files.
- **test:**  Optional, adds or updates automated tests.
- **chore:** Optional and to be considered - for things like updating configuration files, renaming files without changing the logic, metadata\versioning or dependency updates, CI/CD tasks.


**Guidelines:**

* Keep commit messages concise and to the point.
* Use the imperative mood (e.g., "Add," not "Added").
* Provide sufficient context to understand the changes.
* Reference the relevant request or change number.
* Always add a space after the ":" and before the request number.
* Keep the length of the first line under 72 characters (general standard, visibility without wrapping).

### Branch Naming Convention:

- Use `{action}/{request-id}-{short-description}`
- The **request ID (RITMxxxxx or CHGxxxxx)** is **mandatory**
- Use **kebab-case** (lowercase, hyphen-separated) for readability

**Examples:**

- `feat/RITM-1234-include-previous-day-data`
- `fix/CHG-5678-correct-data-mapping`
- `refactor/RITM-3456-optimize-data-transform`
- `docs/RITM-7890-update-readme`
- `test/RITM-2345-add-order-processing-tests`

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