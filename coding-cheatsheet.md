# coding-cheatsheet

This is a cheatsheet to improve the quality of life when coding. I only list common used ones. For a complete git cheatsheet, please check [this repository](https://github.com/arslanbilal/git-cheat-sheet).

### Table of Content
- [Linux operations](#linux-operations)
- [git operations](#git-operations)
- [conda operations](#conda-operations)
- [pip operations](#pip-operations)
- [postgresql operations](#postgresql-operations)

# <a name="linux-operations"></a>Linux operations
## Basic Linux operations
Create a SSH key pair under "~/.ssh".
```sh
ssh-keygen -t rsa -b 4096
```

# <a name="git-operations"></a>git operations
## Basic git operations
Clone a repository with all submodules:
```sh
git clone --recursive [REPOSITORY_URL]
```
Check status
```sh
git status
```
Add the changes of only one file
```sh
git add [YOUR_FILE_NAME]
```
Add all changes (be careful when using this command since you may add many unrelated files to the repository)
```sh
git add .
```
Undo the step of adding changes
```sh
git reset
```
Undo the step of adding changes of one file
```sh
git reset [YOUR_FILE_NAME]
```
Create a commit. Before commiting changes, always check the list of files that you just add (by using "git status").
```sh
git commit -m "Add your commit here."
```
Push code to the remote repository
```sh
git push
```
Check the differences between the current state and the previous commit
```sh
git diff
```
List branches
```sh
git branch
```
Create a branch and switch to it
```sh
git checkout -b [NEW_BRANCH_NAME]
```
Switch to a specific branch
```sh
git checkout [BRANCH_NAME]
```
Delete a local branch
```sh
git branch -D [BRANCH_NAME]
```
Pull the commits from the main branch
```sh
git pull
```
Rebase the local branch based on the main branch
```sh
git pull --rebase
```
Stash current changes (so that you can pull or rebase)
```sh
git stash
```
Pop the stashed changes back
```sh
git stash pop
```
## Combined git operations
### Commit flow
Commit the changes of files to a branch in the repository
```sh
git status
git diff
git add [FILE_1_TO_ADD]
git add [FILE_2_TO_ADD]
# ... keep adding files
git status
git commit -m "[COMMIT MESSAGE]"
git push
```
### Branch rebase flow
Rebase a feature branch based on the main branch (so that the changes in the main branch is pulled to the feature branch).
```sh
git checkout main
git pull --rebase
git checkout [FEATURE_BRANCH]
git rebase main
git pull --rebase
git push
```
### Merge a remote branch into the current branch and preserve commit history, using the periscope-public-engagement-tool remote main branch as an example
```sh
git remote add -f periscope-public-engagement-tool https://github.com/TUD-KInD/periscope-public-engagement-tool.git
git merge periscope-public-engagement-tool/main --allow-unrelated-histories
git push
git remote rm periscope-public-engagement-tool
```

# <a name="conda-operations"></a>conda operations
Activate an environment
```sh
conda activate [ENVIRONMENT_NAME]
```
Deactivate an environment
```sh
conda deactivate
```
List all environments
```sh
conda env list
```
Create an environment
```sh
conda create -n [ENVIRONMENT_NAME]
```
List the installed packages in an environment
```sh
conda list
```
Remove an environment
```sh
conda env remove -n [ENVIRONMENT_NAME]
```
Remove a conda package from an environment
```sh
conda remove -n [ENVIRONMENT_NAME] [PACKAGE_NAME]
```

# <a name="pip-operations"></a>pip operations
Install a package
```sh
pip install [PACKAGE_NAME]==1.1.2
```
Update a package
```sh
pip install --upgrade [PACKAGE_NAME]
```
Install a package with version number
```sh
pip install [PACKAGE_NAME]==[VERSION_NUMBER]
```
Update a package to a version number
```sh
pip install --upgrade [PACKAGE_NAME]==[VERSION_NUMBER]
```
Remove a package
```sh
pip uninstall [PACKAGE_NAME]
```

# <a name="postgresql-operations"></a>postgresql operations
Access the postgres superuser
```sh
# For Ubuntu
sudo -u postgres psql postgres

# For Mac OS
psql postgres
```
Remove postgresql
```sh
# For Ubuntu
dpkg -l | grep postgres
sudo apt-get --purge remove [package_names]

# For Mac OS
brew remove postgresql
```
Start postgresql
```sh
sudo systemctl start postgresql
```
Stop postgresql
```sh
sudo systemctl stop postgresql
```
Restart postgresql
```sh
sudo systemctl restart postgresql
```
Stop the postgresql
```sh
sudo systemctl stop postgresql
```
