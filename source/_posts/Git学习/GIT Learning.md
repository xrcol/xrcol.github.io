---
title: GIT学习
date: 2024-04-21 12:08:15
tags: GIT
categories: GIT
---

## GIT Learning

### 提交

```bash
git commit -m xxx
```

### 创建并切换分支

```bash
git branch xxxx # 创建分支
git switch xxx # 切换分支
git checkout -b xxx # 创建并切换分支
```

### 合并分支

```bash
git merge xxx # 合并指定分支
git rebase xxx # 将提交移动到指定分支
```

### 分离HEAD

```bash
git switch commitid # 切换到指定的提交记录
git checkout commitid
```

### 相对引用

```bash
git checkout main^ # 切换到main分支前一个提交
git checkout HEAD~3
git branch -f main HEAD~2 # 移动main分支到head前两个提交
```

### 回退提交

```bash
git reset xxx 
git revert xxx # 会产生一个新的提交
```

### 合并提交

```bash
git cherry-pick xx xx xx # 将其它分支的提交按顺序提交到main分支上
git rebase -i xx xx xx # 交互式调整提交记录
```

### 创建tag

```bash
git tag tagname xx # 基于分支名或hash创建tag
```

### 离当前提交最近的tag

```bash
git describe xxx # 输出离当前提交最近的tag tagname_distance_gcurrenthash
# v1_2_gC6
```

### 多个父节点

```bash
git branch bugWork HEAD^2^ # 取HEAD的第二个父节点，再往上取上一个节点
```

### 合并远程分支

```bash
git pull --rebase # 不会产生新的提交
git fetch && git rebase o/main
git pull # 会产生新的提交
git fetch && git merge o/main
```

### 远程跟踪

```bash
# 使用checkout
git checkout -b foo origin/main
# 使用branch -u
git branch -u origin/main foo
```

### push参数

```bash
git push origin source:destination
git push origin foo^:main 
# 如果destination不存在，则会默认创建分支
```

### fetch参数

```bash
git fetch origin source:destination
# 如果destination不存在，则会默认创建分支
git fetch origin main:bar
```

### 删除远程分支

```bash
git push origin :foo # 删除远程分支foo
git fetch origin :bar # 本地创建分支
```

