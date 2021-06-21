---
title: "Git 相关"
tags: ["Git"]
categories: ["编程 · 技术"]
date: "2020-04-10"
cover : /img/timg.jpg
---

### Git 相关操作

------

1、git清空密码

```
git config --system --unset credential.helper
```

2、git保存密码

```
git config --global credential.helper store
```

3、没有加入暂存区 后退一步

```
git checkout 
```

4、加入暂存区 后退一步

```
git reset
```

5、查看提交日志

```
git log
```

6、返回相对应的日志号

```
git reset --hard + git log 中的日志号
```

7、设置国内源 and 查看原

```
npm set registry https://registry.npm.taobao.orgnpm config list
```

8、查看当前git分支

```
git branch
```

9、拉取Git项目

```
git pull origin master
```

10、素质三联

```
git add -Agit commit -m ""git push -u origin master
```

------

### 一、版本控制系统

------

1、意义 记录文件内容变化，以便将来查阅特定版本修订情况的系统 可以讲文件回溯到某个修改之前的状态。 版本控制不仅对源码做控制，可以对任意类型文件进行版本控制

2、常用的版本工具 CVS SVN Git-分布式 首先安装 文本编辑器软件，使用notepad++ 作为文本编辑器

3、git本地版本管理

## 4、git本地仓库与远程仓库通信

### 二、git本地版本管理

------

1、配置我们的身份

```
$ git config --global user.name "John Doe"$ git config --global user.email johndoe@example.com
```

2、初始化一个git仓库 选择一个文件夹 作为我们的仓库 然后初始化它

```
git init
```

3、查看git当前的状态

```
git status
```

我们目录下的文件 有两种状态： 未追踪untracked文件 已追踪tracked文件 已追踪 表示 这个文件已经添加到git仓库中 未追踪 表示 文件还没有添加到仓库中

4、添加未追踪的文件

```
git add 文件
```

5、把 add的文件 进行commit提交 添加到仓库

```
git commit 
git commit -m "提交的说明" 不会打开文本编辑器
```

总结：一个新文件 要先 add 在 commit 两步才能提交到仓库

6、忽略文件

项目中 不是所有的文件 都需要追踪的，比如一些临时文件，日志就不用追踪

因此我们可以写一个.gitingore文件

7、我们的git可以分为三个区域

工作区：我们从仓库中 提取某个版本 作为工作内容，对文件可以修改、删除等操作

暂存区：工作区中的文件内容发生改变，我们要把这个改变先保存起来，放到暂存区 等待提交

仓库区：所有的已经提交commit的数据

如果一个被追踪的文件 发生了修改则操作过程：

第一、git add 文件/* 会把文件从工作区 拷贝一份 放到暂存区

第二、git commit 文件 会把暂存区存放的所有文件 都提交到仓库

------

### 三、本地仓库与 远程仓库

------

1、从远程仓库 clone 到本地

这个过程 会自动把远程仓库的所有内容 都拷贝一份到本地，并生成创建一个对应的本地仓库

```
$ git clone 链接
```

本地仓库名默认就是 bookweb

2、推送项目到远程仓库

git push 推送 推送本地仓库内容 到远程仓库

3、push的时候因为要 登录远程仓库，所有需要认证

第一次的时候需要你输入 github的 账号 密码

我们在输入完以后可以通过 命令

①设置记住密码（默认15分钟）：

```
git config --global credential.helper cache
```

②如果想自己设置时间，可以这样做(1小时后失效)：

```
git config credential.helper 'cache --timeout=3600'
```

③长期存储密码：

```
git config --global credential.helper store
```

4、代码冲突的问题

想在开发都是 团队协同开发有可能多人同时修改了文件中的某一处

解决冲突： 第一步 首先要先 git pull 先把远程的新的提交 拉过来，到本地仓库。

5、标签tag 了解

```
给一个提交版本 添加 标签  用来表示该提交git tag -a 标签名  -m "描述标签"git tag -a v1 -m "版本1"
```

6、git分支

查看分支

```
git branch
```

创建分支 `git branch 分支名` 切换分支

```
git checkout 分支
```

合并分支：

注意 谁合并谁 先切换到谁
master要合并dev 先切换到master

```
git checkout mastergit merge dev
```

删除分支

```
git branch -d dev
```