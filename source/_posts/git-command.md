---
title: git 命令生存指北
date: 2018-06-21 02:03:13
tags: git
---

> 整理一下用 `git` 命令解决一些问题的方法，后面会持续更新。

### 怎么回滚 OR 撤销

#### Case1: 想要撤销代码在工作区的修改

```bash
//撤销工作区某个文件的修改(恢复暂存区的文件到工作区)
git checkout <file> <file2>...

//撤销工作区所有文件的修改
git checkout .

//删除工作区新增的某个文件
git clean -df <file> <file2>...

//删除工作区所有新增文件
git clean -df
```

#### Case2: 想要撤销代码在暂存区的修改

```bash
git reset HEAD 
```

#### Case3: 想要撤销已经commit的代码

假设如果最后一次 `commit` 是第8次提交， 想要将代码回滚到第5次提交.

方案1:

```bash
1. 首先执行 git log 查看第5次提交的 HEAD 值
2. 然后执行 git reset --hard <HEAD 5>
3. git add . 
4. git commit -m 'revert 6 7 8'
5. 执行git push -f 强制推送

（ 这里值得注意的是，因为第2步重置了之前的commit，如果执行 git push 推送到远程会报错，需要加 -f 强制推送。 
```
> 这里值得注意的是，因为第2步重置了之前的commit，如果执行 git push 推送到远程会报错，需要加 -f 强制推送。
> 由于一般 git 仓库是多人协同开发，你的 commit 可能已经被其他小伙伴合并过，像方案1这样回滚并强制推送并不是好的解决方案，这样做会把工作流弄乱。

方案2:

更好的回滚方法是：

```bash
1. 首先执行 git log 查看第5次提交的 HEAD 值
2. 然后执行 git reset --hard <HEAD 5>
3. 然后执行 git log 查看最后一次（第8次）提交的 HEAD 值
4. 然后执行 git reset --soft <HEAD 8>
5. git commit -m 'revert 6 7 8'
6. 执行git push -f 强制推送

（ 上面的做法把回滚的动作当作了一次新的提交，不会打乱git工作流。
```


### 怎么解决冲突

```bash
//检查冲突
grep -rl '<<<<<' 

1.  //冲突的文件采用引入分支的修改 
    git checkout -- ours <file>
2.  //冲突的文件采用当前分支的修改
    git checkout -- theirs <file>
3.  其他的文件手动解决
```

### 怎么暂存代码

当你在 `A分支` 上工作时，有人来告诉你说 `B功能` 有些地方需要调整，这时候需要先把 `A分支` 的代码储存起来，然后切到 `B分支`。

```bash
//储存当前工作区的内容
git stash

//从栈中恢复最近一次保存的内容（执行之后这条内容从栈中移除）
git stash pop

//从栈中恢复最近一次保存的内容（执行之后这条内容不会从栈中移除）
git stash apply

//显示栈列表的内容
git stash list

//清空栈
git stash clear
```

### 有哪些可以提高效率的命令

```bash
//切换到上一个分支
git checkout -
```

`oh-my-zsh` 的一些常用命令缩写:

```bash
alias ga='git add'
alias gb='git branch'
alias gba='git branch -a'
alias gbd='git branch -d'
alias gcam='git commit -a -m'
alias gcb='git checkout -b'
alias gco='git checkout'
alias gcm='git checkout master'
alias gcp='git cherry-pick'
alias gd='git diff'
alias gfo='git fetch origin'
alias ggpush='git push origin $(git_current_branch)'
alias ggsup='git branch --set-upstream-to=origin/$(git_current_branch)'
alias glgp='git log --stat -p'
alias gm='git merge'
alias gp='git push'
alias gst='git status'
alias gsta='git stash save'
alias gstp='git stash pop'
alias gl='git pull'
alias glg='git log --stat'
alias glgp='git log --stat -p'
alias glgga='git log --graph --decorate --all' 
```

