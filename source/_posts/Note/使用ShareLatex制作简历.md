---
title: "使用ShareLatex和Python3打造属于自己的特色简历"
tags: ["ShareLatex","Python"]
categories: ["总结 · 笔记"]
date: "2020-07-11 16:59:18"
cover : /img/notepic.jpg
---

### 序幕

**履历**(英式英语：[Curriculum Vitae](https://zh.wikipedia.org/w/index.php?title=Curriculum_Vitae&action=edit&redlink=1)，简称**CV**；美式英语：[Résumé](https://zh.wikipedia.org/w/index.php?title=Résumé&action=edit&redlink=1))，是对个人教育、工作经历的书面介绍，是求职者通向面试阶段的重要一环。

说起简历模板，大家一定不会陌生，随便在百度搜关键词“简历模板”，林林总总的会出现一大片，但是这些简历模板往往都会有一个共同点，就是太Low、“土味”重、没有时代感、味同嚼蜡，蜡都要顺着嘴角流下来了，以一个求职者的视角来看待这些简历都会无奈心烦，更别说招聘者了。所以新时代的简历应该具备独特性、新颖、与众不同并且不落窠臼。

Resume是在申请求职时最常使用的文件，为你的教育程度、工作经历以及工作技能做简单且明了的摘要，同时，也会依个人、申请职位的需求，列出求职目标。由于是个人“摘要”，Resume通常为一页，最多不超过二页，以求简洁。

而在CV中，则会详细的列举出个人的经验与相关技能，尤其是个人的学术背景，如：教学经验、研究成果、获奖纪录、相关出版物⋯⋯等细节，也因此，CV的篇幅会比Resume长上许多，通常会超过二页。

### 开始操作

1. 首先进入[ShareLatex](https://www.sharelatex.com/)，注册账号并登陆。

2. 之后通过邮箱激活，选择新建序幕，并选简历

   ![sha](https://wangxs020202.gitee.io/images/note/sha1.png)
   
3. 紧接着我们会发现有许多简历模板，但是都用LaTeX语法写的

   ![sha](https://wangxs020202.gitee.io/images/note/sha2.png)

4. 随后点开其中一个，就可以进行定制化的编辑

   ![sha](https://wangxs020202.gitee.io/images/note/sha3.png)

5.  编辑之后，可以点击重新编译查看效果，如果编辑好之后就可以在线下载pdf，非常方便

   ![sha](https://wangxs020202.gitee.io/images/note/sha4.png)

6. 但是由于LaTeX语法我们没有学过，而且编辑成本比较高，最重要的对于一些英文不好的很难编辑。

7. 其实`6`的问题很好解决。我们可以下载这上面的原始pdf简历，再通过Python脚本将其转换为我们所熟悉的Word文档模式，这样就可以随便进行编辑

8. 通过Pdfminer3k以及Python-Docx两个库，使用python可以实现

9. 首先安装依赖

   ```python
   pip3 install pdfminer3k
   pip3 install python-docx
   ```

10. 通过Pdfminer3k读取pdf内容，再使用Python-Docx写入word文档

    ```python
    from pdfminer.pdfinterp import PDFResourceManager
    from pdfminer.pdfinterp import process_pdf
    from pdfminer.converter import TextConverter
    from pdfminer.layout import LAParams
    from io import StringIO
    from docx import Document
    
    
    file = open("pdf文件地址", 'rb')
    
    resource_manager = PDFResourceManager()
    return_str = StringIO()
    lap_params = LAParams()
    
    device = TextConverter(resource_manager, return_str, laparams=lap_params)
    process_pdf(resource_manager, device, file)
    device.close()
    
    content = return_str.getvalue()
    
    
    def remove_control_characters(content):
        mpa = dict.fromkeys(range(32))
        return content.translate(mpa)
    
    
    doc = Document()
    for line in content.split('\n'):
        paragraph = doc.add_paragraph()
        paragraph.add_run(remove_control_characters(line))
    doc.save("mypdf.docx")
    ```

11. 但是，这样弄出来的简历，没有样式而且麻烦

12. 我们可以通过注册登陆[https://smallpdf.com/](https://smallpdf.com/)此网站，进行在线转换

    ![sha](https://wangxs020202.gitee.io/images/note/sha5.png)

13.  直接拖拽上传即可。

    ![sha](https://wangxs020202.gitee.io/images/note/sha6.png)

### 结语

一个不落俗套的简历模板可以让你的求职如虎添翼，也可以让你的简历从招聘者邮箱中的海量简历中脱颖而出，但是简历模板也仅仅是求职中的一个重要细节之一，比起简历模板，简历中的工作经历以及技术经验则更加重要，切不可本末倒置。