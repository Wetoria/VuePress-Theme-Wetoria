---
title: 个人git知识总结
tag: 
---
个人常用的git命令以及相关知识总结，具体git命令的介绍可以查看[官网](https://git-scm.com/)。

<!-- more -->

## 1.常用命令一览

```
// 每个命令下的缩进为该命令下的一些参数或操作
1.init
2.status
3.diff
  head
4.add
  .
  dir-path
  file-path
5.commit
  -m
  --amend
6.rebase
  -i head~n
7.log
  -p
  --graph
8.reflog
9.checkout
  -b branch-name
  -- dir-path/file-path
10.branch
  -a
  -d branch-name
  -D branch-name
11.merge branch-name
12.pull
  origin branch-name
13.push
  --set-upstream origin branch-name
14.reset
  --hard commit-hash
  head dir-path/file-path
15.stash
  pop
```

## 2.常用命令使用场景
### 1.init  

> 新建项目时，初始化一个文件夹作为git仓库。

### 2.status

> 查看被修改的文件有哪些。

**使用场景：在每次提交代码前使用，用于确认修改过的文件有哪些，避免提交用于测试而不需要提交的文件的前置操作。**

具体使用场景：对接后端开发环境修改了`axios.js`的配置，为避免影响线上环境，不应提交该文件，并在提交其他功能相关的文件以后，应回退对`axios.js`的修改。

### 3.diff

> 查看文件中具体的修改。`diff`命令将会显示`工作区`中文件的具体修改差异，经过`add`操作的文件，需要使用`diff head`查看具体差异。

**使用场景：在提交代码前，确认每处修改是否正确，以及代码中是否有多余的修改。**

具体使用场景：我在进行`add`和`commit`操作前，会使用`diff`/`diff head`对文件进行检查，看是否有`console.log`没有删除。
(我们的项目中开发环境允许使用`console.log`进行调试，但在`commit`前会对修改过的文件中是否留有`console.log`进行检查。)还会用来检验是否有错别字，或者在某个不经意间敲了键盘，增加了多余的代码。

### 4.add
> 用于将`工作区`中的文件添加至`暂存区`，为`commit`操作做准备。可以使用`add .`添加所有`工作区`中的文件，也可以使用`add '文件路径'`/`add '文件夹路径'`，添加单个文件或单个文件夹路径下的所有文件。

使用场景：`commit`前，将`工作区`中的文件添加至`暂存区`。

具体使用场景：`add s/p` => `tab 补全` => `add src/public`，将`public`文件夹下的文件添加至暂存区。**这里主要是想介绍通过`文件路径`/`文件夹路径`添加特定的文件。**

### 5.commit

> 提交暂存区中的文件，形成一次提交记录。平时主要使用`commit -m '对本次提交的说明'`，说明中注明本次提交所涉及的相关功能，以便于在日后的维护中，可以便捷的了解本次提交做了什么操作。

**使用场景：功能或任务完成后，提交相应文件。**

#### 5.1 --amend

> 修改上一次`commit`中的错误。如果因为手误导致提交了错误的`commit`信息，可以使用`commit --amend`修改上一次的提交信息。

#### 5.2 commit时机

如果想更好的对版本进行管理，需要控制好commit的时机，如果每修改一行代码就进行一次提交，将能对整个git仓库中的文件进行行级别的版本控制。不过这种操作方式很不便利，个人使用的是实现某个功能后，便对代码进行一次提交，当对下一个功能进行开发时，遇到阻碍时，则可以使用`git checkout -- src`回退所有的修改，从头再来，这样可以保持每段时间内只修改部分功能，而不会出现一次开发过程中存在大量文件被修改，提交时忘记去除临时代码，以及避免一段时间开发后直接`commit -a`。如何避免开发一个功能存在n多提交信息的问题，请看下一节`rebase`命令。

### 6.rebase -i head~n

> 对n个最新的提交进行操作。使用该命令后，会pick最新的n条提交记录，通过将`pick`修改为[`edit`|`squash`|`fixup`]，对提交记录进行[`修改`|`合并`|`合并并丢弃该提交`]

使用场景：
- 1.修改某个项目组成员的某个提交。
- 2.功能开发完毕以后，将多个提交合并成一个。

### 7.log

> 显示当前分支的提交历史。  
>`-p`参数：显示提交历史以及文件中具体的修改。  
>`--graph`参数：显示提交历史以及分支操作的图标形式。

### 8.reflog

> 显示当前仓库所有的操作历史，除了提交记录，还有分支操作记录。

### 9.checkout

> 切换分支/回滚文件。  
> `checkout branch-name`：切换分支。  
> `checkout -b branch-name`：将会创建并切换至该分支。  
> `checkout -- '文件路径'/'文件夹路径'`：回滚单个文件，或文件夹下的所有文件。
> 在远程仓库新建了`branchA`，在本地拉取代码以后，可以通过`checkout branchA`直接基于远程分支创建并切换至`branchA`。

### 10.branch

> `branch`：显示当前分支及本地所有分支。  
> `branch -a`：显示当前分支以及本地及远程仓库的所有分支。  
> `branch branch-name`：创建`branch-name`分支。  
> `branch -d branch-name`：删除`branch-name`分支。如果分支被合并了以后，可以进行删除操作，否则会报错。
> `branch -D branch-name`：强制删除`branch-name`分支。

### 11.merge

> `merge branch-name`：将`branch-name`分支合并至当前分支。  

### 12.pull

> `pull`：在设置了上游分支的情况下，拉取当前分支在远程仓库中的最近版本到本地分支。
> `pull origin branch-name`：拉取远程仓库中的`branch-name`的最新版本到当前本地分支。

推荐在每天上午开始工作前、中午、晚上进行`pull`操作，防止没有拉取新代码导致合并时出现冲突。项目使用敏捷看板进行项目管理，每次开发新任务前会进行`pull`操作，以保证开发新功能时，本地代码是最新版本。

### 13.push

> `push`：在设置了上游分支的情况下，将当前分支推送至远程仓库中设置的上游分支。如果远程仓库中，上游分支被删除了，该操作会重新创建对应的新分支。  
> `push --set-upstream origin branch-name`：将`branch-name`分支推送至远程仓库中，并将远程仓库中的`branch-name`设置为该分支的上游分支。

### 14.reset

> `reset --hard commit-hash`：回退代码至指定`commit-hash`的版本。需要进行回退操作时，通过`log`命令查询需要回退的代码版本，记录`commit`的`hash`值，一般取前七位就可以，与`reflog`保持一致。如果要回到最新版本，使用`reflog`找到最新版本的`hash`，然后`reset --hard latest-hash`即可。  
> `reset head file-path/dir-path`：将`暂存区`中的文件移至工作区。`add`了错误的文件至`暂存区`时，可以通过该命令移出。

### 15.stash

> `stash`：“临时储藏”当前修改。有时候做了文件修改以后，需要进行切换分支或者`pull`操作时，会提示有未提交的修改，可以通过该命令储藏当前修改，在进行了如`pull`等其他操作以后，再通过`git stash pop`恢复储藏的代码。  

## 3.总结

这篇文章记录了个人开发过程中常用的一些git命令，以及对其的一些理解，本文没有记录命令的具体效果，只记录了一些自己经常使用的情况。其中对于 **`commit`时机、 通过文件夹路径`add`文件、 通过`status`以及`diff`确认修改的文件及内容**，是作为一个好的开发人员，在协同工作中需要注意的地方。