title: Git Command
---

# Git Command

#### Restore files

```sh
$ get checkout HEAD <filename>

#! discard changes in working directory
$ get checkout -- <filename>
```

#### Show all branches

```sh
$ git branch -a
```

#### Create a local-tracking branch

```sh
#! git branch <branchname> [<start-point>]
$ git branch <branchname> <origin/branch_name>
$ git checkout <branchname>
```

Combined into a single step with:
```sh
$ git checkout -b <branchname> <origin/branch_name>
```

#### Rename branch

```sh
$ git branch -m <oldname> <newname>
```

Rename a remote-tacking branch and the corresponding reflog:
```sh
$ git branch -m <oldbranch> <newbranch>
$ git push origin :<oldbranch>
$ git push origin <newbranch>
```

#### Add remote repository

Push an existing repository from the command line:
```sh
$ git remote add <originname> <url>
$ git push -u <originname> <branchname>
```

```sh
$ git branch --set-upstream-to=origin/<branchname> <localbranchname>
```

#### Delete the remote-tracking branch

```sh
$ git push origin --delete <branchname>
```

#### Delete tag

Delete existing tags with the given names:
```sh
$ git tag -d <tagname>...
```

Delete remote tag:
```sh
$ git push origin master :refs/tags/<tagname>

#! After Git v1.7.0
$ git push origin --delete tag <tagname>

#! Push an empty branch to the remote branch
$ git push origin :<branchname>
```

#### Local tag push to remote

```sh
$ git push origin master --tags
```

#### Get remote tag

```sh
$ git fetch origin tag <tagname>
```

#### Version back

```sh
$ git reset --hard <commit>
$ git push -f
```

#### Squashed commits

```sh
$ git checkout master

#! 执行一下命令后，dev上的所有提交已经合并到当前工作区并暂存，但还没有作为一个提交
$ git merge --squash dev

$ git commit –m 'something from dev'
```

#### Delete untracked files

```sh
$ git clean -nfd
$ git clean -fd
```

- `-f, --force`
- `-d`: Remove untracked directories in addition to untracked files

#### Modify commit message

```sh
$ git stash
$ git rebase HEAD^ --interactive
$ git commit –amend
$ git rebase –continue 
$ git push -f
```

#### Clone a specific branch or tag

```sh
$ git clone --branch=v2.0.2.RELEASE https://github.com/spring-cloud/spring-cloud-netflix.git
```