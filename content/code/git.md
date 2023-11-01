+++
title = 'Git'
date = 2023-11-01T19:44:13+08:00
draft = true
+++

<aside> 💡 夜路走多了总会碰见鬼的

</aside>

## Getting started

### Add ssh key

```bash
# generate ssh and add it to you github
git config --global user.name "your-name"
git config --global user.email "your.name@email.com"
ssh-keygen -t rsa -C "your.name@email.com"

# clone a project which you **forked**
git clone your-forked-project
cd your-forked-project
# add upstream project
git remote add upstream upstream-project

# add change and push to your repo
git add .
git commit -m "commit msg"
git push origin your-current-branch[:origin-branch]

# sync upstream branch to local
# option 1: git pull
git pull upstream master # master is you want to sync

# option 2: git fetch & git rebase
git fetch upstream master
git rebase upstream/master

# Any question you can RESOURCE: git [command] --help
# ==== Add personal github/gitlab account =======
ssh-keygen -t rsa -C "personal-github-account@email.com"
ssh-add ~/.ssh/id_rsa_personal
# Could not open a connection to your authentication agent.
eval "$(ssh-agent)
ssh-add ~/.ssh/id_rsa_personal
# Identity added: /c/Users/leih.CORPDOM/.ssh/id_rsa_personal (personal-github-account@email.com)
# if show:  WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!
# delete the know_hosts file under .ssh and try command again

```

## hub — create PR

```bash
# export|grep GITHUB_TOKEN
hub pull-request -f -b <org>:master -h <org>:test1 -m test -l test

# title use commit msg 
hub pull-request --no-edit

# <https://github.com/github/hub/issues/154>
# set default branch when pull-request
git remote set-head origin <new-branch>
# OR
git remote set-head origin -a
```

## git de-bug *bisect*[!!!](https://git-scm.com/book/zh/v1/Git-工具-使用-Git-调试)

1. `git bisect start`
2. `git bisect bad`
3. `git bisect good [good commit]`
4. find it and fixed. use `git bisect reset` return the begin

## 配置了`.gitignore`但是并没有起作用

solution**:**

```
git rm --cached *.class
git commit -m "add gitignore file"
```

## Good Habit

- 本地新建分支，然后merge到master上去，用master作为提pr的branch

- 最好是开始写代码之前rebase代码, 省的merge conflict. Rebase before or after change does not matter, the point is, do not rebase branch which other guys or other mechine working on it.

- 在add之前，看status`git status`

- 在add之前，看diff `git diff` 最好看diff是用**source tree**

- 注意**编码**；github上是LF，win上是CRLF **坑注意**

  ```bash
  cat .gitconfig
  [user]
          name = xx
          email = xx@xx.com
  [difftool "sourcetree"]
          cmd = '' \\"$LOCAL\\" \\"$REMOTE\\"
  [mergetool "sourcetree"]
          cmd = "'' "
          trustExitCode = true
  [http]
          sslVerify = false
  [http "<https://github.com>"]
          proxy = <http://proxy:8080>
  [http "<https://gitlab.com>"]
          proxy = <http://proxy:8080>
  
  [core]
          autocrlf = input
          eol = lf
  [color "status"]
          updated = green bold
  [color "branch"]
          current = green bold
  [alias]
    fc = !sh -c 'git fetch $1 $2 && git checkout $(cut -d':' -f2 <<<"$2")' -
    fr = !sh -c 'git fetch $1 $2 && git rebase $1/$2' -
  [hub]
          host = enterprise-github-host
  ```

- can use `git option --help`

## rebase

```
git fetch upstream
//rebase branch which you want, most is default branch
// Do not rebase branch which other guy work on it
git rebase upstream/master
...
//if not use rebase, use pull also a good choice but cause a merge node created
git pull origin master
```

### merge code 第一要义

> 如果pull/rebase 之后会有大量的代码冲突, 这个时候最好的方式不是用IDEA 或者 vscode 去帮你解决冲突, 而是 : 把你的改动拉出来, 到upstream最新点,然后再把改动放回去

为什么这么说呢?

1. 冲突的话, 用intellij/vscode 改着改着,就很有可能卡死了, 而且, 解决大量冲突是一件非常繁琐的事情.
2. 当你基于一个遥远的节点工作之后, 你无法保证这你工作的这些文件的这些点是不是会被后面的commit 给冲掉.

## commit

- 前提

> 多个commit合并成一个commit(这些'多个commit'指只做一件事的commit) `pr` 尽可能的细一个pr，一个分支只能做一件事file change 最好不要超过10个, `git commit --amend --no-edit` 补提交，但不增加commit 节点 love it: `git commit --amend change your commit msg`

```bash
git reset commit-hash
相当于
git reset --soft HEAD~
```

- **cherry-pick** apply commit-c from branch-A to branch-B

  ```bash
  git checkout branch-b
  git cherry-pick commit-c-hash
  ```

  这样就会在 branch-b 后面再加个 commit-c

- `add .` but except one file OR assume file unchanged

  refer: https://stackoverflow.com/questions/10879783/git-doesnt-ignore-2-specifically-named-files

```bash
git update-index --assume-unchanged <file-path-want-to-ignore>

# OR execute below every commit

git add .
git reset HEAD <FILE>
```

## 拉pr到本地分支

```bash
git fetch <remote-name> pull/<pr-id>/head:<new-branch>
git checkout repack-org-apache
```

> 在checkout之前不要手动创建这个 repack-org-apache 分支

[RESOURCE](https://www.huangyunkun.com/2018/06/15/pull-github-pr-to-local-branch/)

## 懒人 git stash

- git stash
- git stash apply
- git stash pop

## submodule

`git submodule add url folder` 在一个module里面添加ignore的submodule，顺序是很重要的。 我在其中还看到了empty repo的一些特性。

1. submodule remote repo 不能为空
2. `.gitignore` 必须在添加完 submodule 之后才能把这个 folder 添加上去，要是之前就添上去了咋搞，指路`git rm --cached`
3. update submodule 指路 `git submodule --help`

## remote repo

1. remote repo 不能为空,empty repo cannot be created

## RESOURCE

- [RESOURCE](https://git-scm.com/book/zh/v1/Git-工具-储藏（Stashing）)
- [git bisect](https://git-scm.com/book/zh/v1/Git-工具-使用-Git-调试)
- [submodule](https://git-scm.com/book/zh/v1/Git-工具-子模块)
