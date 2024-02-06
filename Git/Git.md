# Git

[Rename a Local and Remote Git Branch](#rename-a-local-and-remote-git-branch)
[Combine multiple commits into one](#combine-multiple-commits-into-one)
[Check branch contains commit](#check-branch-contains-commit)
[Pull force](#pull-force)

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

### Combine multiple commits into one

Suppose that you want to merge the last 3 commits into a single commit. 
To do that, you should run git rebase in interactive mode (-i) providing the last commit to set the ones that come after it. 
Here, HEAD is the alias of the very last commit.
```
git rebase -i HEAD~3
```

After the first step, the editor window will show up offering you to input the command for each commit. 
All you need to do is `replacing pick with squash`, starting from the second line. Then, save the file.

One more editor window will show up to change the resulting commit message. 
Here, you can find all your commit messages and change them according to your exact needs.

If you have already pushed your commits, then you should force push them using the git push command with — force flag

### Check branch contains commit
```
$ git branch --contains <commit-id>
```

### Pull force
```bash
git checkout master
git branch new-branch-to-save-current-commits
git fetch --all
git reset --hard origin/master
```