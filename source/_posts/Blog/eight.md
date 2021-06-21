---
title: "递归与无限极评论"
tags: ["Python"]
categories: ["编程 · 技术"]
date: "2020-04-30"
cover : /img/timg.jpg
---


### 递归算法

**什么是递归？**

1. 在数学与见算计科学中，是指在函数的定义中使用函数自身的方法。
2. 递归算法就是一种直接或者间接的调用自身函数或者方法的算法
3. 递归算法的实质是把问题分解规模小的同类问题的子问题
4. 然后调用自身方法来解

**递归的基本原理**

1. 每一级的函数调用都有自己的变量
2. 每一次函数调用都会有一次返回
3. 递归函数中，位于递归调用前的语句和各级被调用函数具有相同的执行顺序
4. 递归函数中，位于递归调用后的语句的执行顺序和各个被调用函数的顺序相反
5. 虽然每一级递归都有自己的变量，但是函数的代码不会得到复制

**递归的优缺点**

优点

- 实现简单
- 可读性好

缺点

- 递归调用，占用空间大
- 递归太深，容易发生栈溢出
- 可能存在重复计算

**递归的三大要素**

1. 明确函数要做什么
2. 寻找递归结束条件
3. 找出函数的等价关系式

## python对递归的使用

**解决最大递归深度**

```python
import sys

sys.setrecursionlimit(3000)
```



**高斯求和**

```python
def count_number(n):
    if n <= 0:
        return 0
    return n + count_number(n-1)
  
def count_Number2(n):
  sum = 0
  for i in n:
    sum += i
  return sum

```



**斐波那契**

```python
def fn(n):
  if n == 1:
    return 1
  else if n ==2:
    return 1
  else:
    return fn(n-1)+fn(n-2)
```



## 无限级评论

`[{},{},{},{},{}]`

[{    "child":  {"child":[ {} , {} ]    }     },  {id:5, child:[{ child:[{}],{}]    }   ]

django将数据封装为树结构

```python
def change_comments(data):
    list = []
    tree = {}
    root = ''
    p_id = ''

    for i in data:
        #将data循环，然后加入一个dict中，key为每条数据的ID，val对应为整条数据
        tree[i["id"]] = i
				#{  1:{id:1},  2:{id:2},  3:{id:3}  }  
        # print(tree)

    for i in data:
      #p_id==None，他就是祖先
        if i["p_id"] == None:
            root = tree[i["id"]]  #i.di为tree里的key，将key对应的val取出
            list.append(root)
        else:
            p_id = i["p_id"]
            # 判断父级是否有孩子字段（childlist），如果有将当前数据加入，如果没有添加（childlist）后再加入
            if "childlist" not in tree[p_id]:
                tree[p_id]['childlist'] = []
            tree[p_id]["childlist"].append(tree[i["id"]])

    return list
```



vue使用父子组件递归展示

```vue
<!-- zizujian.vue -->

<template>
    <div>
        <li>{{data.content}}
            <ul v-if="data.childlist && data.childlist.length>0">
            <zi v-for="i in data.childlist" :key="i.id" :data="i" />
            </ul>
        </li>
    </div>
</template>


<script>
export default {
    name:"zi",
    props:["data"]
}
</script>

<style>
    
ul{
    list-style: none;
    /* padding-left: 20px */
}

</style>
```



```vue
<!-- fuzujian.vue -->

<template>
    <div>
        <zi :data="data" />
        <!-- <zizujian :data="data" /> -->
    </div>
</template>


<script>
import zi from './zi'
import axios from 'axios'

export default {
    name:"showzi",
    data() {
        return {
            data:''
        }
    },
    components:{
        zi
    },
    methods:{
        get_info(){
            axios.get("http://127.0.0.1:8000/comment/com/").then(res=>{
                var mytree = {"id":0,"content":""}
                mytree["childlist"] = res.data.data
                this.data= mytree
                // this.data= res.data.data
                // console.log(this.data)
            })
        }
    },
    created(){
        this.get_info()
    }
}
</script>
```


