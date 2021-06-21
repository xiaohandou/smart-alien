---
title: "使用Git Bash实现Git代码上传加密"
tags: ["Git"]
categories: ["总结 · 笔记"]
date: "2020-07-15 16:59:18"
cover : /img/notepic.jpg
---

### 序幕

以前我都是在[gitee](https://gitee.com/)上上传本地项目，今天新创建了一个vue项目，突发奇想，想上传[github](https://github.com/),觉得和gitee差不多，很好实现。谁知道在坑无数啊

### 坑点

1. 这个博客就是在github上部署的，先前用gitbash生成的`id_rsa`用到了这个上面，然后我就再次使用gitbash生成了新的`id_rsa`（由此处去坑）

2. 在我把新的`id_rsa`上传的新项目的时候，推送代码的时候出现了错误

   ```python
   ERROR: Permission to ***** denied to deploy key
   ```

   **实在头疼**

3. 然后在网上找问题。。。

4. 最后将博客下的`ssh key` 放的用户下就ok了

### 解决办法

1. 首先我们需查看本地是否以生成`id_rsa`

   ![](https://wangxs020202.gitee.io/images/note/bash.png)

2. 如果没有使用`Git Bash 进入 ssh 目录`

   ```python
   cd ~/.ssh
   ```

3. 查看自己的github上上传的邮箱

   [https://github.com/settings/emails](https://github.com/settings/emails)

   ![](https://wangxs020202.gitee.io/images/note/bash2.png)

4. 粘贴下面的文本（替换为您的 GitHub 电子邮件地址）。

   ```shell
   $ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
   ```

   这将创建以所提供的电子邮件地址为标签的新 SSH 密钥。

   ```shell
   > Generating public/private rsa key pair.
   ```

5. 提示您“Enter a file in which to save the key（输入要保存密钥的文件）”时，按 Enter 键。 这将接受默认文件位置。

   ```shell
   > Enter a file in which to save the key (/c/Users/you/.ssh/id_rsa):[Press enter]
   ```

6. 在提示时输入安全密码。

   ```shell
   > Enter passphrase (empty for no passphrase): [Type a passphrase]
   > Enter same passphrase again: [Type passphrase again]
   ```

7. 之后查看本地是否生成`id_rsa`，文本编辑器打开公钥 `id_rsa.pub` 复制内容，添加到 Github setting。

   ![](https://wangxs020202.gitee.io/images/note/bash3.png)

   ![](https://wangxs020202.gitee.io/images/note/bash4.png)

8. 完成上述进本已将完成，剩下的就是线上仓库与本地建立联系，推送了。

9. 每次推送需要输入我们当时设置的密码**如何解决？？**

10. git bash 进入你的项目目录，输入： 

    ```shell
    git config --global credential.helper store
    ```

    然后你会在你本地生成一个文本，上边记录你的账号和密码。当然这些你可以不用关心。 然后你使用上述的命令配置好之后，再操作一次 git pull，然后它会提示你输入账号密码，这一次之后就不需要再次输入密码了。

### 结语

本次踩坑实属对Git的理解还是太浅。。。。

[Git菜鸟](https://www.runoob.com/git/git-tutorial.html)