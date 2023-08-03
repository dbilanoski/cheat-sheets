# Git Notes

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