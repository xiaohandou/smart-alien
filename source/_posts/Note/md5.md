---
title: "md5数据加密"
tags: ["Django","Python"]
categories: ["总结 · 笔记"]
date: "2020-06-16 13:54:10"
cover : /img/notepic.jpg
---

### 序幕

**MD5信息摘要算法**（英语：MD5 Message-Digest Algorithm），一种被广泛使用的[密码散列函数](https://baike.baidu.com/item/密码散列函数/14937715)，可以产生出一个128位（16[字节](https://baike.baidu.com/item/字节/1096318)）的散列值（hash value），用于确保信息传输完整一致。MD5由美国密码学家[罗纳德·李维斯特](https://baike.baidu.com/item/罗纳德·李维斯特/700199)（Ronald Linn Rivest）设计，于1992年公开，用以取代[MD4](https://baike.baidu.com/item/MD4/8090275)算法。这套算法的程序在 RFC 1321 标准中被加以规范。1996年后该算法被证实存在弱点，可以被加以破解，对于需要高度安全性的数据，专家一般建议改用其他算法，如[SHA-2](https://baike.baidu.com/item/SHA-2/22718180)。2004年，证实MD5算法无法防止碰撞（collision），因此不适用于安全性认证，如[SSL](https://baike.baidu.com/item/SSL/320778)公开密钥认证或是[数字签名](https://baike.baidu.com/item/数字签名/212550)等用途。

### 实现

#### vue

下载md5包`npm install js-md5`

```
"js-md5": "^0.7.3"
```

测试，并打印出md5

```javascript
<template>
  <div>
	666
  </div>

</template>
<script>
import md5 from 'js-md5'

export default {
  data () {
    return {
    
    }
  },
  //注册组件标签
  components:{
  },
  mounted:function(){
    //  设置秘钥，增加安全性  (应放在配置文件)
    var ed = '2020'
     //price:总价 goodid:商品id(3,1)
	var sign = md5('price=500&goodid=3,1' + ed)
    console.log(sign);
},
  methods:{

  }
}
</script>

<style scoped>

</style>
```

打印结果：

```
a4299afde799fa1bf6d1ba0c13b0def7
```

#### Django(后端)

```python
import hashlib

#修改price
price = "1"
goodid = "3,1"
#前端传的sign
sign = "a4299afde799fa1bf6d1ba0c13b0def7"

#实例化
md5 = hashlib.md5()
#组合要加密的字符串
sign_str = "price="+price+"&goodid="+goodid+"2020"
#解码
mysign = str(sign_str).encode(encoding="utf-8")
#设置加密
md5.update(mysign)
mysign = md5.hexdigest()
#判断
print(mysign)
if sign == mysign:
	print("pass")
else:
	print("数据被篡改")
```

打印结果：

```
8c6849857cfeab25fa83bf7497943022
数据被篡改
```

### 总结

md5加密不经常用，但在设计到重要信息(金钱，虚拟币，商品)的时候会给我们数据带来非常安全的保障