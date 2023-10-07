# Git

[Rename a Local and Remote Git Branch](#rename-a-local-and-remote-git-branch)

### Rename a Local and Remote Git Branch

Follow the steps below to rename a Local and Remote Git Branch:

1. Start by switching to the local branch which you want to rename:

`git checkout <old_name>`

2. Rename the local branch by typing:

`git branch -m <new_name>`

At this point, you have renamed the local branch.
If you’ve already pushed the <old_name> branch to the remote repository, 
perform the next steps to rename the remote branch.

3. Push the <new_name> local branch and reset the upstream branch:

`git push origin -u <new_name>`

4. Delete the <old_name> remote branch:

`git push origin --delete <old_name>`

That’s it. You have successfully renamed the local and remote Git branch.