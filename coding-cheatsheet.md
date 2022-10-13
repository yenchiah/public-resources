# coding-cheatsheet

This is a cheatsheet to improve the quality of life when coding. I only list common used ones. For a complete git cheatsheet, please check [this repository](https://github.com/arslanbilal/git-cheat-sheet).

### Table of Content
- [Linux operations](#linux-operations)
- [git operations](#git-operations)
- [conda operations](#conda-operations)
- [pip operations](#pip-operations)
- [postgresql operations](#postgresql-operations)
- [Others](#others)

# <a name="linux-operations"></a>Linux operations
## Basic Linux operations
Create a SSH key pair under "~/.ssh" and add the key
```sh
ssh-keygen -t rsa -b 4096 -C "your_email@youremail.com"
ssh-add ~/.ssh/id_rsa
```
If ssh-add does not work, run the following line before running ssh-add:
```sh
ssh-agent bash
```
Unzip a tar file
```sh
tar -xzvf [FILE_NAME]
```
Zip a file or folder with password
```sh
zip -e archivename.zip filetoprotect
zip -er archivename.zip foldertoprotect
```
Get the total size of a folder with name "video"
```sh
du -sh videos/
```
Show disk usage
```sh
df -h
```
## Partition and mount disks
First need to get the drive path (e.g., /dev/sda, /dev/sdb, /dev/sdc).
```
sudo fdisk -l
```
Then, partition the drive. Here we use "/dev/sdb" as an example.
```
sudo parted /dev/sdb
```
Now run the following inside the parted shell. Command "mklabel gpt" means creating the GUID Partition Table. Command "mkpart primary 0% 100%" means creating a partition that uses the entire drive. You can specify the disk size, for example, by using "mkpart primary 0TB 4TB".
```
mklabel gpt # the GUID Partition Table 
unit TB
mkpart primary 0% 100%
print
quit
```
Next run the following to check the exact drive path (e.g., /dev/sdb1, /dev/sdb2)
```
sudo fdisk -l
```
Run the following to mount the disk drive partition. Here we use an example to mount "/dev/sdb1" to "/workspace".
```
sudo mkdir /workspace
sudo mkfs -t ext4 /dev/sdb1
sudo mount /dev/sdb1 /workspace
```
Now run the following to check if the disk partition is there, and reboot the machine. It is very important to reboot the machine, as we will need the drive's UUID later. Without rebooting, the UUID can be wrong.
```
df -h
sudo reboot now
```
After rebooting, run the following to get the partition's UUID (using /dev/sdb1 as the example).
```
blkid /dev/sdb1
```
Finally, we need to put the disk partition information in fstab. See [here](https://wiki.archlinux.org/title/fstab) for the fstab documentation.
```
sudo vim /etc/fstab

# Add the following to the file
UUID=[uuid] /workspace ext4 defaults,noatime 0 0

# Below is an example
# UUID=849hyr87-j83y-89jd-3j7s-89jyh3gtd70v /workspace ext4 defaults,noatime 0 0
```
To check if it is working, reboot the system and check the disk status (in this example, "/workspace" should exist after rebooting).
```
sudo reboot now
df -h
```
### Fix improper LVM partitions that do not use the full disk
The solution is obtained from [this link](https://askubuntu.com/questions/1106795/ubuntu-server-18-04-lvm-out-of-space-with-improper-default-partitioning).
```
# We need to resize the logical volume to use all the existing and free space of the volume group
$ lvm
lvm> lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv
lvm> exit

# And then, we need to resize the file system to use the new available space in the logical volume
$ resize2fs /dev/ubuntu-vg/ubuntu-lv
resize2fs 1.44.1 (24-Mar-2018)
Filesystem at /dev/ubuntu-vg/ubuntu-lv is mounted on /; on-line resizing required
old_desc_blocks = 1, new_desc_blocks = 58
The filesystem on /dev/ubuntu-vg/ubuntu-lv is now 120784896 (4k) blocks long.

# Finally, you can check that you now have available space:
$ df -h
Filesystem                         Size  Used Avail Use% Mounted on
udev                               3.9G     0  3.9G   0% /dev
tmpfs                              786M  1.2M  785M   1% /run
/dev/mapper/ubuntu--vg-ubuntu--lv  454G  3.8G  432G   1% /
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
Revert the changes of the file to the HEAD
```sh
git checkout HEAD [FILE_NAME]
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
Add submodule
```sh
git submodule add [REPOSITORY_URL]
```
Edit the local repository config
```sh
git config -e --local
```
Hide ^M symbol at the end of line in git diff
```
git config --global core.whitespace cr-at-eol
```
Configure Git to ensure line endings in files you checkout are correct for macOS
```
git config --global core.autocrlf input
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
### Merge a remote branch into the current branch and preserve commit history, using the periscope-public-engagement-tool remote repository as an example
```sh
git remote add -f periscope-public-engagement-tool https://github.com/TUD-KInD/periscope-public-engagement-tool.git

# If we want to preserve history
git merge periscope-public-engagement-tool/main --allow-unrelated-histories

# If we want to squash the commits
git merge periscope-public-engagement-tool/main --allow-unrelated-histories --squash
git commit

git push
git remote rm periscope-public-engagement-tool
```
### Add a remote branch as a [subtree](https://www.atlassian.com/git/tutorials/git-subtree) in the current repository (squash the commit history), using the periscope-public-engagement-tool remote repository as an example
```sh
# Initialize the subtree
git subtree add --prefix periscope-public-engagement-tool https://github.com/TUD-KInD/periscope-public-engagement-tool.git main --squash
git push

# Update the subtree
git subtree pull --prefix periscope-public-engagement-tool https://github.com/TUD-KInD/periscope-public-engagement-tool.git main --squash
git push
```
You can simplify the workflow by adding the sub-repository as a remote:
```sh
# Initialize the subtree
git remote add -f periscope-public-engagement-tool https://github.com/TUD-KInD/periscope-public-engagement-tool.git
git subtree add --prefix periscope-public-engagement-tool periscope-public-engagement-tool main --squash
git push

# Update the subtree
git fetch periscope-public-engagement-tool main
git subtree pull --prefix periscope-public-engagement-tool periscope-public-engagement-tool main --squash
git push
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

# <a name="others"></a>Others
Set the style of the vim editor
```
vim Style
cat ~/.vimrc

:color desert
syntax on
set showmatch
set expandtab
set softtabstop=2
set shiftwidth=2
set autoindent
set number
filetype indent plugin on
set pastetoggle=<F12>
filetype plugin on
set omnifunc=syntaxcomplete#Complete
vmap <leader>y :w! /tmp/vitmp<CR>
nmap <leader>p :r! cat /tmp/vitmp<CR>
autocmd BufWritePre *.py %s/\s\+$//e
```
Set the style of inputrc
```
cat ~/.inputrc

"\e[B": history-search-forward
"\e[A": history-search-backward
```
Set the terminal style
```
# For mac:
cat ~/.bash_profile

# For ubuntu:
cat ~/.profile

# Add color in terminal
export CLICOLOR=1
export LSCOLORS=ExGxBxDxCxEgEdxbxgxcxd
export PS1='\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '
if which rbenv > /dev/null; then eval "$(rbenv init -)"; fi
export PATH="/usr/local/miniconda3/bin:$PATH"
. /usr/local/miniconda3/etc/profile.d/conda.sh
export LIBRARY_PATH="$LIBRARY_PATH:/usr/local/opt/openssl/lib/"
```
