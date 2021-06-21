---
title: "Win10系统下安装编辑器之神Vim"
tags: ["Vim"]
categories: ["总结 · 笔记"]
date: "2020-07-10 16:59:18"
cover : /img/notepic.jpg
---

### 序幕

相对于pycharm，Sublime、Vscode等编辑器，vim一直是处于编辑器的最顶端，奉行 Unix 传统的“Do one thing and do it well”哲学。

本次我们在Win10平台构建一套以Vim为核心的Python开发环境。

### 下载安装与使用

1. 首先进入[giv官网](https://tuxproject.de/projects/vim/x64/)下载gvim8，注意根据系统类型选择32或者64位，这里我们选择64位的

   ![vim](https://wangxs020202.gitee.io/images/note/vim1.png)
   
2.  下载完成后，将下载好的压缩包解压，并且将文件放到C:/vim目录下

   ![vim](https://wangxs020202.gitee.io/images/note/vim2.png)

3. 紧接着我们配置下环境变量，这样我们就可以在电脑的任意位置启动vim

   ![vim](https://wangxs020202.gitee.io/images/note/vim3.png)

4. 之后在当前的用户目录，建立一个_vimrc文件，这是vim的配置文件，所有的设置都在这里编写

   将以下内容添加到_vimrc文件中

   ```python
   " An example for a vimrc file.
   "
   " Maintainer:	Bram Moolenaar <Bram@vim.org>
   " Last change:	2019 Dec 17
   "
   " To use it, copy it to
   "	       for Unix:  ~/.vimrc
   "	      for Amiga:  s:.vimrc
   "	 for MS-Windows:  $VIM_vimrc
   "	      for Haiku:  ~/config/settings/vim/vimrc
   "	    for OpenVMS:  sys$login:.vimrc
   
   " When started as "evim", evim.vim will already have done these settings, bail
   " out.
   if v:progname =~? "evim"
     finish
   endif
   
   " Get the defaults that most users want.
   source $VIMRUNTIME/defaults.vim
   
   if has("vms")
     set nobackup		" do not keep a backup file, use versions instead
   else
     set backup		" keep a backup file (restore to previous version)
     if has('persistent_undo')
       set undofile	" keep an undo file (undo changes after closing)
     endif
   endif
   
   if &t_Co > 2 || has("gui_running")
     " Switch on highlighting the last used search pattern.
     set hlsearch
   endif
   
   " Put these in an autocmd group, so that we can delete them easily.
   augroup vimrcEx
     au!
   
     " For all text files set 'textwidth' to 78 characters.
     autocmd FileType text setlocal textwidth=78
   augroup END
   
   " Add optional packages.
   "
   " The matchit plugin makes the % command work better, but it is not backwards
   " compatible.
   " The ! means the package won't be loaded right away but when plugins are
   " loaded during initialization.
   if has('syntax') && has('eval')
     packadd! matchit
   endif
   
   set encoding=utf-8
   set fileencodings=utf-8,chinese,latin-1
   if has("win32")
       set fileencoding=chinese
   else
       set fileencoding=utf-8
   endif
   
   set autoindent
   set nu!
   set shiftwidth=4
   
   source $VIMRUNTIME/delmenu.vim
   source $VIMRUNTIME/menu.vim
   
   language messages zh_CN.utf-8
   
   colo koehler
   set guifont=monaco:h11:cANSI
   
   set ts=4
   set expandtab
   
   map <F5> :! python.exe %
   ```

5. 这些都是一些最基本的配置，比如设置编码解决中文乱码问题、自动缩进以及缩进宽度、菜单栏中文字体问题、主题和字体、以及四个空格代替制表符等等，注意一点这个配置里我将运行python脚本的快捷键设置成了F5。

   这时进入windows命令行，输入gvim启动编辑器，然后键入命令:version，看到版本号就没有问题了

   ![vim](https://wangxs020202.gitee.io/images/note/vim4.png)

6. 虽然现在Vim已经可以正常使用了，但是没有插件的加成，开发效率就不是那么高，所以我们现在来安装一些常用的插件。安装[pathogen.vim插件](https://github.com/tpope/vim-pathogen)（一个vim插件管理器， 直接Clone或者下载压缩包将Clone或者解压后的pathogen.vim文件放到C:/vim/autoload目录下

   ![vim](https://wangxs020202.gitee.io/images/note/vim5.png)

7. 修改用户目录下的_vimrc配置文件，将下面的配置加进去

   ```
   execute pathogen#infect()
   ```

8. 这样就可以安装其他所有的插件了，紧接着我们安装一个[项目管理插件](https://www.vim.org/scripts/script.php?script_id=69)(project)，它可以帮助我们把项目整体导入vim编辑器内，通过点击文件进行修改，这样就不用每次编辑都要在命令行输入命令才能编辑了，大体上，这个插件可以帮我们快速修改整个项目。

   ![vim](https://wangxs020202.gitee.io/images/note/vim6.png)

9. 将解压后的doc目录中的project文件拷贝到vim安装目录的doc目录下将plugin目录下的project.vim拷贝到vim安装目录的plugin目录下在命令行输入gvim启动编辑器。输入:Project，随后输入\C (是反斜杠和大写C，因为是输入命令，所以不会在编辑内显示，但是执行成功后会弹出窗口)

   ![vim](https://wangxs020202.gitee.io/images/note/vim7.png)

10. Enter the Name of the Entry: 输入项目名，Enter the Absolute Directory to，Load: 输入项目的文件目录路径（项目目录需要事先存在），Enter the CD parameter: 这个和项目目录路径一样即可，Enter the File Filter: 设置管理的文件类型，*.py,*.txt等等，可以设置多个，不设置（直接回车）默认为所有类型

    再次使用：打开vim后输入:Project
    使用回车打开或关闭标签。
    添加或者修改文件后可以使用\R进行刷新项目。

    这样我们就可以在vim里管理我们的项目了。

    ![vim](https://wangxs020202.gitee.io/images/note/vim8.png)

11. 好了，项目导入后就可以愉快的开发了，但是我们发现vim默认没有代码补全，怎么办呢，聪明如你一定已经猜到可以用插件搞定，使用[pydiction](https://github.com/rkulla/pydiction)Clone或者下载压缩包之后，发现里面有after文件夹、complete-dict、pydiction.py

    将after里面的python_pydiction.vim文件拷贝到 vim安装目录下的ftpplugin里面，将complete-dict、pydiction.py 拷贝到ftpplugin目录下。

    ![vim](https://wangxs020202.gitee.io/images/note/vim9.png)

    随后在_vimrc里面添加 

    ```python
    filetype plugin on
    let g:pydiction_location='C:vim/ftplugin/complete-dict'
    let g:pydiction_menu_height = 3
    ```

    这就搞定了，使用方法是，敲入两个字母之后使用tab键进行补全，效果是下面这样：

    ![vim](https://wangxs020202.gitee.io/images/note/vim10.png)

12. 还不错吧，有的时候，你甚至想用vim来编辑前端的页面，没有任何问题，使用[autocomplpop插件](https://vim.sourceforge.io/scripts/script.php?script_id=1879)

    解压后，将plugin下的脚本文件(.vim)、doc下的帮助文件(.txt)和autoload下的(.vim)文件分别拷贝至vim的 plugin、doc和autoload目录

    这个插件甚至不需要配置，只需要在输入/insert模式下即可自动根据当前文档内的内容进行自动补全

    ![vim](https://wangxs020202.gitee.io/images/note/vim11.png)

13. 是不是感觉还不错？有了那么一点黑客的赶脚了。

    Vim 有两种模式——Normal 模式和 Insert 模，所有命令都是在 Normal 模式下执行。启动 Vim 后，默认进入 Normal 模式，可以按 i 键进入 Insert 模式，或者 s 删除当前字符并进入 Insert 模式，退出 Insert 模式进入 Normal 按 ESC 。

    基本用法：

    ```python
    i insert 输入
    v 行选中
    ctrl+v 列选中G 至文末
    gg 至文首
    :q 未修改退出
    :q! 强制不保存退出
    :x / :wq 保存并退出
    J 合并多行
    d 删除当前所选
    dd 删除多行并存在剪贴板中（剪切）
    y 复制当前所选
    yy 复制整行
    p 粘贴
    u 撤销操作
    w 光标移动到下一个单词处
    b 光标移动到上一个单词处
    ^ 光标移动到行首
    $ 光标移动到行尾
    kjhl 或者上下左右键移动光标
    shift+上下键 翻页
    shift+左右 光标乙至上/下一个单词（以空格/标点区分单词）词首
    u 撤销上一步操作
    zo/zn/zc 折叠/展开代码块
    :vsp 新建工作区
    ctrl+w 松手后再按 方向键 切换工作区
    :MR 选择最近打开的文件（需安装插件）
    F12 运行当前文件
    # 搜索光标处短语
    :set paste 进入粘贴模式
    :%s/target/something/g 替换全部 target 字段
    :s/target/something/g 替换选中区域 target 字段
    ```

14.  参考命令图解

    ![vim](https://wangxs020202.gitee.io/images/note/vim12.png)

### 结语

现而今，Mac os和开源软件渐渐流行起来，此时的人们才发现：可扩展性才是软件的核心竞争力。在JetBrains横行的今天，Vscode为什么被评为最好的IDE？就是因为它在IDE中最具可扩展性。同理，将近30多年的历史浪潮中Vim没有被时代淘汰，反而愈发健壮，拥趸遍布全世界，也正是因为在数不清的编辑器中，Vim具有无可匹敌的可扩展性，当然了，这个世界除了编辑器之神，还有另外一种信仰：Emacs，它的教徒丝毫不少于Vim，它的影响力已经是超越编辑器的存在，有机会再分享关于Emacs的传说。