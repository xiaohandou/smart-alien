---
title: "Vue中两种跳转方式"
tags: ["Vue"]
categories: ["总结 · 笔记"]
date: "2020-04-13"
cover : /img/notepic.jpg
---


读书忌死读,死读钻牛角。——叶圣陶

第一种：通过标签跳转，

![link](https://wangxs020202.gitee.io/images/me/link.png)

第二种：通过js跳转，定义点击事件进行跳转

![js1](https://wangxs020202.gitee.io/images/me/js1.png)![js2](https://wangxs020202.gitee.io/images/me/js2.png)

第三种：通过标签跳转，

```
<a href="添加需要跳转的路由"></a>
```

如果想要新页面跳 不覆盖前一页面 可在a标签内加 target=”_blank”

```
<a href="添加需要跳转的路由" target="_blank"></a>
```