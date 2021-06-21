---
title: "虚拟环境的下载操作"
tags: ["Python"]
categories: ["编程 · 技术"]
date: "2020-04-06"
cover : /img/timg.jpg
---

### 下载虚拟环境控制器

```
pip install virtualenvwrapper-win -i https://pypi.tuna.tsinghua.edu.cn/simple
```

### 创建虚拟环境

```
mkvirtualenv  +name
```

### 切换虚拟环境

```
Workon +name
```

### 下载包

```
Pip install +name(包名称)
```

### 删除包

```
Pip uninstall +name(包名称)
```

### 删除虚拟环境

```
Rmvirtualenv +name
```

### 导出当前环境所有包

```
Pip freeze > requirements.txt（>> 追加）
```

### 批量下载包

```
Pip install -r  requirements.txt（路径）
```

### 退出当前虚拟环境

```
deactivate env_+name
```

### 查看安装的所有虚拟环境

```
lsvirtualenv
```

### 查看当前环境所有包

```
Pip list
```