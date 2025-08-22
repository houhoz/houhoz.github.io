---
title: Git 入门操作命令
author: Leohoo
pubDatetime: 2021-06-22 15:11:26
slug: git-operation-commands-guide
featured: false
draft: false
tags:
  - Git
  - 版本控制
  - 开发工具
description: Git版本控制完整操作指南，涵盖stash、分支管理、标签操作、冲突处理等常用命令和实用技巧。
---

## Table of contents


### git stash

git stash 会把所有未提交的修改（包括暂存的和非暂存的）都保存起来，用于后续恢复当前工作目录。

```bash
# 暂存
git stash
# 恢复之前缓存的工作目录（将缓存堆栈中的第一个stash删除，并将对应修改应用到当前的工作目录下）
git stash pop
# 查看
git stash list
# stash 添加提交记录
git stash save "第一次stash"
# 默认弹出第一个 但不会删除 可以添加参数应用哪一个 stash@{0}
git stash apply
#移除stash 具体stash@{0}
git stash drop
# 删除所有的stash
git stash clear
```

### git配置

### 配置 user 信息（设置仓库管理员信息）

```bash
# 为本地所有仓库的设置配置user的name信息
git config --global user.name 'your_name'
# 为本地所有仓库的设置配置user的email信息
git config --global user.email 'your_email@domain.com'
```

### 使用 --local 专门配置当前仓库的配置信息。

```bash
# 修改 name为your_name
$ git config --local user.name 'your_name'
# 修改email为另一个邮箱地 your_email@domain.com
$ git config --local user.email 'your_email@domain.com'
```

### 查看配置

`git config --global --list`

### SSH key生成

```bash
# 生成一个 ssh-key
# -t: 可选择 dsa | ecdsa | ed25519 | rsa | rsa1，代表加密方式
# -C: 注释，一般写自己的邮箱
ssh-keygen -t rsa -C "your_email@domain.com"
cd ~/.ssh
```

## git分支操作

### 创建分支 切换分支

```bash
# 创建本地分支
git branch <BranchName>
# 切换本地分支
git checkout <BranchName>
# 创建并切换分支
git checkout -b  <BranchName>
# 作用是checkout远程的dev分支，在本地起名为dev分支，并切换到本地的dev分支
git checkout -b dev origin/dev
git checkout -b feat/album-lock origin/feat/album-lock
```

### 查看分支

```bash
# 查看本地分支
git branch
# 查看所有分支
git branch -a
# 查看远程分支
git branch -r
```

### 推送分支到远程

```bash
git push origin <BranchName>
# 不在当前分支，在当前分支，直接 git push
```

### 删除分支

```bash
# 删除本地分支
git branch -d <BranchName>
# 删除远程分支
git push origin --delete <BranchName>
git push origin :<BranchName>
```

### 合并分支

```bash
git merge <要合并的分支>
# 如果合并之后还未push 想要回退
# 查看要回退的hash版本号
git reflog
git reset --hard [hash]
```

### 回退

```bash
git reset --hard [hash]
git push origin daily/0.0.3 -f
git reset --soft|--mixed|--hard <commit_id>
--mixed
# 意思是：不删除工作空间改动代码，撤销commit，并且撤销git add . 操作
# 这个为默认参数,git reset --mixed HEAD^ 和 git reset HEAD^ 效果是一样的。
--soft
# 不删除工作空间改动代码，撤销commit，不撤销git add .
--hard
# 删除工作空间改动代码，撤销commit，撤销git add .
# 注意完成这个操作后，就恢复到了上一次的commit状态。
```

```bash
git status -- 查看状态
git init ---初始化一个文件夹作为仓库
git add ---将文件添加进暂存区
git rm --cached . -r(表示递归) ---删除暂存区的全部内容
git commit -m 'xxx' --- 将暂存区添加到版本库
git reset --soft HEAD^ --- 撤销上次的提交
git commit --amend --- 如果commit注释写错了，只是想改一下注释
git log --- 查看消息
git diff --- 默认比较工作区和暂存区
git diff master（分支名称，不一定是master） --- 比较工作区和版本库
git diff --cached -- 比较暂存区和版本库
git checkout .

git 批量删除本地分支
git branch -a | grep "^  feature*" | xargs git branch -D
批量删除本地分支
git branch | grep -E 'fixbug|feature' | xargs git branch -D
```

### git tag

```bash
# 列出标签
git tag
git tag -l "v1.0.0*"
#创建标签
git tag v1.0.0
git tag -a v1.0.0 -m "my version 1.0.0" # 附注标签
git tag -a v1.0.1 9fceb02 # 后期打标签
# 删除标签
git tag -d <tagname> # 本地
git push origin --delete <tagname> # 远程
# 共享标签
git push origin <tagname>
git push origin --tags # 一次性推送很多标签
# 检出标签
git checkout 2.0.0
git checkout -b version2 v2.0.0 # 修复旧版本中的错误,需要创建一个新分支

# 在已有tag修改bug
git checkout v1.27.30
git cherry-pick <commit id>
git tag v1.27.30-<xxx>
git push origin v1.27.30-<xxx>
```

## git后悔操作

1. 在未暂存前，撤销本地修改

    本地编写的代码，不执行任何git命令，处于工作区。执行`git add`命令时，会将工作区的文件标记为已暂存，保存在暂存区。执行`git commit`命令时，会将标记为已暂存的文件保存都本地Git仓库，并生成一个快照。

    在没有暂存之前（没有执行git add命令），我们可以通过以下命令查看本地修改：

    `git diff`

    不想要这些代码本地代码（比如一些临时的测试代码）一次性撤销所有本地修改：

    `git checkout -- .`

    我们也可以指定具体的文件路径，撤销该文件的修改：

    `git checkout -- public/index.html`

2. 在暂存之后，撤销暂存区的修改

    本地写完代码，提交到本地仓库之前，需要先将修改的文件添加到暂存区，执行以下命令将本地所有已修改的文件添加到暂存区（当然也可以指定具体的文件）：

    `git add .`

    此时我们执行git diff命令，将不会有任何输出（因为文件已被添加到暂存区），想要查看暂存区的修改，可以执行以下命令：

    `git diff --staged`

    看到的效果和之前为暂存前，通过git diff看到的一模一样。

    如果这时我们想要一次性撤销暂存区的全部修改，可以执行以下命令（当然也可以撤销暂存区指定文件的修改）：

    `git reset .`

    1和2的两个命令可以合并成一个：

    `git reset --hard`

    <=>

    `git reset .`

    `git checkout --`

3. 提交到本地仓库之后（但未推送到远程仓库），撤销本次提交

    执行以下命令，可以将暂存区的所有文件保存到本地Git仓库，并生成文件快照（便于之前的回退等操作）：

    `git commit -m "modify some files"`

    此时提交历史里面会有一条记录dd73404（Commit ID）：

    - 方法一
        - 回到当前提交的父对象dd73404（即上一次提交，通过git log查看），就等于撤销了本次提交：
        - `git checkout dd73404`
    - 方法二
        - `git reset --hard HEAD~1`
4. 修改提交

    ```bash
    git add src/app/app.component.html
    git commit -m "add test block"
    git log
    # 这样其实只提交了app.component.html一个文件，不是我们想要的。
    # 可以通过以下命令“反悔”（添加遗漏文件，又不重新生成新的Commit ID）：
    git add src/app/app.component.css
    git commit --amend

    # 还有一种场景可能更加常见，没有遗漏的文件，只是提交信息里有一个单词写错了，可以使用以下命令进行修补：
    git commit --amend -m "add test container"
    ```

5. 合并出现冲突时，撤销合并操作

    `git merge --abort`

6. 暂存区的文件加多了，想移除，又不想删掉本地的文件

    `git rm --cached src/test.pptx`

7. 分支重命名

    `git br -m [old_br] [new_br]`

8. git pull 冲突处理失败返回到pull之前的状态（版本回退）

    ```bash
    git reflog
    git reset --hard HEAD@{0}
    # 或者
    git reset --hard 337b5a0
    ```


### 常见错误

1. git remote: HTTP Basic: Access denied 错误
    - 如果账号密码有变动 用这个命令
    - `git config --system --unset credential.helper`
    - 重新输入账号密码 应该就能解决了
2. Git中git pull/push 老是重复输入用户名密码
    - 在终端下执行
    - `git config --global credential.helper store //长期保存面`
    - 可以看到~/.gitconfig文件，会多了一项 [credential] helper = store

[git rebase (1)](https://www.notion.so/git-rebase-1-1fd71e08a282807fac3bcf9a3248af1b?pvs=21)
