# coding-cheatsheet

This is a cheatsheet to improve the quality of life when coding. I only list common used ones. For a complete git cheatsheet, please check [this repository](https://github.com/arslanbilal/git-cheat-sheet).

### Table of Content
- [Linux operations](#linux-operations)
  - [Basic Linux operations](#basic-linux-operations)
  - [Partition and mount disks](#partition-and-mount-disks)
  - [Fix improper LVM partitions](#fix-improper-lvm-partitions)
- [git operations](#git-operations)
  - [Basic git operations](#basic-git-operations)
  - [Combined git operations](#combined-git-operations)
- [conda operations](#conda-operations)
  - [Basic conda operations](#basic-conda-operations)
- [pip operations](#pip-operations)
- [postgresql operations](#postgresql-operations)

# <a name="linux-operations"></a>Linux operations
## <a name="basic-linux-operations"></a>Basic Linux operations
Add new user on a machine, set a default password for the user, and force the user to set a new password
```sh
sudo useradd [USERNAME]
sudo passwd [USERNAME]
sudo passwd --expire [USERNAME]
```
Give sudo permission to an user
```sh
sudo usermod -aG sudo [USERNAME]
```
Count number of files in a directory
```sh
ls -1 | wc -l
```
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
## <a name="partition-and-mount-disks"></a>Partition and mount disks
First need to get the drive path (e.g., /dev/sda, /dev/sdb, /dev/sdc).
```sh
sudo fdisk -l
```
Then, partition the drive. Here we use "/dev/sdb" as an example.
```sh
sudo parted /dev/sdb
```
Now run the following inside the parted shell. Command "mklabel gpt" means creating the GUID Partition Table. Command "mkpart primary 0% 100%" means creating a partition that uses the entire drive. You can specify the disk size, for example, by using "mkpart primary 0TB 4TB".
```sh
mklabel gpt # the GUID Partition Table 
unit TB
mkpart primary 0% 100%
print
quit
```
Next run the following to check the exact drive path (e.g., /dev/sdb1, /dev/sdb2)
```sh
sudo fdisk -l
```
Run the following to mount the disk drive partition. Here we use an example to mount "/dev/sdb1" to "/workspace".
```sh
sudo mkdir /workspace
sudo mkfs -t ext4 /dev/sdb1
sudo mount /dev/sdb1 /workspace
```
Now run the following to check if the disk partition is there, and reboot the machine. It is very important to reboot the machine, as we will need the drive's UUID later. Without rebooting, the UUID can be wrong.
```sh
df -h
sudo reboot now
```
After rebooting, run the following to get the partition's UUID (using /dev/sdb1 as the example).
```sh
blkid /dev/sdb1
```
Finally, we need to put the disk partition information in fstab. See [here](https://wiki.archlinux.org/title/fstab) for the fstab documentation.
```sh
sudo vim /etc/fstab

# Add the following to the file
UUID=[uuid] /workspace ext4 defaults,noatime 0 0

# Below is an example
# UUID=849hyr87-j83y-89jd-3j7s-89jyh3gtd70v /workspace ext4 defaults,noatime 0 0
```
To check if it is working, reboot the system and check the disk status (in this example, "/workspace" should exist after rebooting).
```sh
sudo reboot now
df -h
```
## <a name="fix-improper-lvm-partitions"></a>Fix improper LVM partitions
Sometimes the LVM partitions may not use the entire available disk space, and we want to fix this. The solution is obtained from [this link](https://askubuntu.com/questions/1106795/ubuntu-server-18-04-lvm-out-of-space-with-improper-default-partitioning).
```sh
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
## <a name="basic-git-operations"></a>Basic git operations
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
## <a name="combined-git-operations"></a>Combined git operations
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
Document for managing conda environments can be found [here](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html).
## <a name="basic-conda-operations"></a>Basic conda operations
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
Export the environment
```sh
conda env export > environment.yml
```
Create an environment from "environment.yml"
```sh
conda env create -f environment.yml
```
To update an environment, edit the contents of "environment.yml" file accordingly and then run the following command
```sh
conda env update --prefix ./env --file environment.yml  --prune
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
