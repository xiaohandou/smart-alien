---

title: "git相关命令"

tags: ["Git"]

categories: ["总结 · 笔记"]

date: "2020-05-27"

cover : /img/notepic.jpg

---

### 一、Git的相关命令以及在公司中git的用处

总所周知[Git](https://www.runoob.com/git/git-tutorial.html) 是一个开源的分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目。它不仅仅是个版本控制系统，它也是个内容管理系统(CMS)，工作管理系统等。

一般我们每到一家公司都会创建属于自己的分支（前提是你们公司使用git开发），因为如果不创建自己的分支我们所写的代码就没有办法保存。所以第一个命令

查看本地分支

```python
git branch
```

通常在克隆下来的代码中只有master的分支，不要慌，我们创建一个develop分支，用于代码的测试

git创建分支

```python
git checkout -b 自己的分支名称（develop）
```

创建完develop分支后创建自己的分支，有的同学会问为什么不在develop分支上直接写代码呢？----原因很简单如果你在develop分支上写完代码提交的时候发现别人也修改了develop分支上的代码，会发生冲突，你说用你的好还是用他的好！！

git分支开发就像漫威宇宙一样，一个主宇宙，多个分宇宙。当其中一个分宇宙发生变化是不会影响到主宇宙和其他分宇宙。

我们在本地创建自己分支后也要在线上创建分支

在线上创建分支

```python
git push --set-upstream origin 分支名称
```

当你一顿操作完之后，就要开始写代码了

先查看当前分支，然后切换到自己的分支

切换分支 

```python
git checkout  分支名称
```

写完代码之后提交到自己的分支（素质三连）

将本地代码暂存

```python
git add -A
```

将本地代码提交 

````python
git commit -m '提交内容'
````

推送本地到线上 

```python
git push origin  分支名称
```

最后是合并代码（这个操作一般不是我们操作）

合并分支代码

```python
git merge 分支名称
```

### 二、Git 快速入门

Git 完整命令手册地址：[https://git-scm.com/docs](https://git-scm.com/docs)

联合开发注意文章：[[https://www.sirxs.cn/2020/04/21/Note/git%E8%81%94%E5%90%88%E5%BC%80%E5%8F%91/](https://www.sirxs.cn/2020/04/21/Note/git联合开发/)]([https://www.sirxs.cn/2020/04/21/Note/git%E8%81%94%E5%90%88%E5%BC%80%E5%8F%91/](https://www.sirxs.cn/2020/04/21/Note/git联合开发/))

 

