---
title: "HEY UI 与 智能输入框"
tags: ["HEY UI" , "Vue"]
categories: ["编程 · 技术"]
date: "2020-04-07"
cover : /img/timg.jpg
---


### 关于HeyUI

HeyUI 是一套基于 Vue2.0 的开源 UI 组件库，主要服务于一些中后台产品。

------

### 特性

HeyUI提供的是一整套解决方案，所有的组件提供全局的可配置模式。

```

●真正的数据驱动

●全局的配置模式

●数据字典化

```

------

### 安装

推荐使用 npm 的方式安装。

```
npm install heyui
```

------

### 支持环境

```

现代浏览器和 IE9 及以上。

```

------

### 兼容

```
HeyUI支持 Vue.js 2.x版本
```

------

### 相关链接

```
 ○ https://vuejs.org/Vue (官方文档)

 ○ https://github.com/heyui/hey-cli (Hey-Cli脚手架)

 ○ https://admin.heyui.top/ (官方demo)
```

------

## 智能搜索(AutoComplete 模糊匹配)

### 异步数据请求

autocomplete有三种数据类型：key,title,object，如果需求更复杂，请监听change事件手动处理。

```
<template>
  <div>
    <p>value:{{value}}</p>
    <div v-width="300"><AutoComplete :option="param" v-model="value" @change="onChange" type="title"></AutoComplete></div>
  </div>
</template>
<script>
import jsonp from 'fetch-jsonp';
const loadData = function (filter, callback) {
  // this 为 option 配置
  // this.orgId 使用传递的参数获取数据，例：搜索某公司下的员工
  jsonp(`https://suggest.taobao.com/sug?code=utf-8&q=${filter}`)
    .then(response => response.json())
    .then((d) => {
      callback(d.result.map((r) => {
        return r[0];
      }));
    });
};
export default {
  data() {
    return {
      value: '测试',
      param: {
        orgId: 1, // 自定义参数传递
        loadData,
        minWord: 1
      }
    };
  },
  methods: {
    onChange(data, trigger) {
      console.log(data, trigger);
    }
  }
};
</script>
```