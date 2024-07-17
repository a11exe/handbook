# Git

[Rename a Local and Remote Git Branch](#rename-a-local-and-remote-git-branch)
[Combine multiple commits into one](#combine-multiple-commits-into-one)
[Check branch contains commit](#check-branch-contains-commit)
[Pull force](#pull-force)
[Show commit](#show-commit)
[Upload changes from branch](#upload-changes-from-branch)
[Remove commit](#remove-commit)
[Show config](#show-config)
[AutoCRLF](#autocrlf)
[Task list between tags](#task-list-between-tags)


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

### Show commit
```bash
git show <commit-id>
```

### Upload changes from branch
```bash
git pull origin develop
```

```bash
git pull --rebase origin develop
```

### Remove commit
Remove last commit
```shell
git reset --hard HEAD^
```
Remove 2 last commits
```shell
git reset --hard HEAD~2
```

if reset --hard doesn't work
```shell
git gc
git reset
git reset --hard <target_branch>
```

### Show config

shows all inherited values from: system, global and local
```shell
git config -l
```

### AutoCRLF
Windows uses both a carriage-return character and a linefeed character for newlines in its files, 
whereas macOS and Linux systems use only the linefeed character. 
This is a subtle but incredibly annoying fact of cross-platform work; 
many editors on Windows silently replace existing LF-style line endings with CRLF, 
or insert both line-ending characters when the user hits the enter key.

Git can handle this by auto-converting CRLF line endings into LF when you add a file to the index, 
and vice versa when it checks out code onto your filesystem. 
You can turn on this functionality with the `core.autocrlf` setting. 
If you’re on a Windows machine, set it to `true` this converts LF endings into CRLF when you check out code:
```shell
$ git config --global core.autocrlf true
```
```shell
add, commit                         checkout
-------------->    Git database  -------------->
 (CRLF -> LF)           (LF)      (LF -> CRLF)
```


If you’re on a Linux or macOS system that uses LF line endings, then you don’t want Git to automatically convert them 
when you check out files; however, if a file with CRLF endings accidentally gets introduced, then you may want Git to fix it. 
You can tell Git to convert CRLF to LF on commit but not the other way around by setting core.autocrlf to input:

```shell
$ git config --global core.autocrlf input
```
```shell
add, commit                         checkout
-------------->    Git database  -------------->
 (CRLF -> LF)           (LF)      (no convertation)
```

If you’re a Windows programmer doing a Windows-only project, then you can turn off this functionality, 
recording the carriage returns in the repository by setting the config value to false:

```shell
$ git config --global core.autocrlf false
```
```shell
  add, commit                             checkout
-------------->    Git database       -------------->
(no convertation) (CRLF and/or LF)    (no convertation)
```

If you're working on Windows with shell scripts, you can add `.gitattributes` file like this:
```shell
docker-compose.yml text eol=lf
Dockerfile eol=lf
*.sh text eol=lf
```

### Task list between tags
```shell
echo 'Please enter start tag'
read startTag
echo 'Please enter end tag'
read endTag
git log --oneline $startTag..$endTag | grep -o MYTAG-[0-9]* | sort --unique
echo 'Task were showed'
```