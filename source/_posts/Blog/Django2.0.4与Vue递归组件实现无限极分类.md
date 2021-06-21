---
title: "Django2.0.4与Vue递归组件实现无限极分类"
tags: ["Django","Python"]
categories: ["编程 · 技术"]
date: "2020-06-03 15:54:10"
cover : /img/timg.jpg
---

### 序幕

什么是无限极分类，按照我的理解，就是对数据完成多次分类，如同一棵树一样，从根开始，到主干、枝干、叶子……

**家谱树**

家谱树是无限极分类的表现形式之一。家谱，现在很多地方都流行起修家谱，那怎么修家谱，按照我理解，就是给自己找一个祖宗，一代代找上去，形成了一个体系，这样编篡而成的叫家谱。家谱树就与之类似，从某个节点开始向上寻找其父节点，再找父节点的父节点，直到找不到为止。按照这种寻找，形成的一个类似树状的结构，就叫做家谱树。

完成无限极分类，主要运用了两种方法，一是递归方式，二是迭代方式。而主要运用无限极分类的地方有商品无限极分类，无限极评论等等。

某博主的无限极评论

![img](https://user-gold-cdn.xitu.io/2017/4/23/69a03461fb2a73909f17fa17a8def891?imageslim)

京东的商品无限极分类

![gitee](https://wangxs020202.gitee.io/images/note/jd.png)

接下来我们先来测试一个层级效果

创建一个测试文件test.py

```python
#自定义一个列表，已设置好层级关系
mylist = [{'id': 1, 'name': '在线课程', 'pid': 0, 'child': [{'id': 2, 'name': 'Python', 'pid': 1}]}, {'id': 3, 'name': 'Django', 'pid': 0}]

#创建一个层级显示函数
def list_dic(d,n_tab=-1):
	# 判断数据
	if isinstance(d,list):
		for i in d:
			list_dic(i,n_tab)
	elif isinstance(d,dict):
		n_tab += 1
		for key,value in d.items():
			print('{}{}'.format('\t'*n_tab,key))
			list_dic(value,n_tab)
	else:
		print('{}{}'.format('\t'*n_tab,d))

list_dic(mylist)
```

效果很明显

![gitee](https://wangxs020202.gitee.io/images/note/cjtest.png)

### 实现课程无限极分类

1. 在Django项目里的models.py创建一个商品模型类(我这里创建的字段有点多，你们可自行选择)

   ```python
   class Courses(models.Model):
    	#主键 通过参数声明主键---必选
       id = models.AutoField(primary_key=True)   
   	# 课程名称---必选
   	name = models.CharField(max_length=200,unique=True)
   	# 描述
   	desc = models.CharField(max_length=200,null=True)
   	# 类别
   	category = models.CharField(max_length=200,null=True)
   	# 课程图片
   	img = models.CharField(max_length=200,null=True)
   	# 层级ID---必选
   	pid = models.IntegerField()
   	# 课程价格
   	price = models.IntegerField(null=True)
   	# 是否删除
   	is_delete = models.IntegerField(default=0,null=True)
   
   	class Meta:
   		db_table = 'course'
   ```

2. 并在数据库中插入数据

   ![gitee](https://wangxs020202.gitee.io/images/note/mysql.png)

3. 由于我的项目基于drf框架，所以需要添加一个序列化器。如果你的项目没有用drf，可以直接用json模块来进行序列化。

   ```python
   #导包
   from rest_framework import serializers
   # 数据库导入
   from myapp.models import Courses
   # 课程序列化器
   class CourseSer(serializers.ModelSerializer):
       class Meta:
           model = Courses
           fields = "__all__"
   ```

4. 接下来写一个递归方法，用来给序列化出来的数据展现出层级结构

   ```python
   def Ctree(data):
   	lists = []
   	tree = {}
   	for item in data:
   		tree[item['id']] = item
   	for i in data:
   		# 判断顶级分类
   		if not i['pid']:
   			lists.append(tree[i['id']])
   		# 子分类
   		else:
   			p_id = i['pid']
   			if 'children' not in tree[p_id]:
   				tree[p_id]['children'] = []
   			# 将子类填充到父类child里
   			tree[p_id]['children'].append(tree[i['id']])
   	return lists
   ```

5. 接下来构造接口，从数据库中获取数据，并调用递归方法

   ```python
   class Course(APIView):
   	def get(self,request):
   		coures = Courses.objects.filter(is_delete=0)
   		coures_ser = CourseSer(coures,many=True).data
   		mylist = Ctree(coures_ser)
   		res = {}
   		res['code'] = 200
   		res['data'] = mylist
   		return Response(res)
   ```

6. 通过测试接口，返回数据效果(由于数据太多不易截图，所以直接复制来了)

   ```python
   {
       "code": 200,
       "data": [
           {
               "id": 1,
               "create_time": "2020-06-03T09:32:14",
               "name": "Python",
               "desc": "Python全套带回家",
               "category": "会员",
               "img": "1.jpg",
               "pid": 0,
               "price": 600,
               "is_delete": 0,
               "children": [
                   {
                       "id": 2,
                       "create_time": "2020-06-03T09:33:02",
                       "name": "Python初级",
                       "desc": "Python初级",
                       "category": "会员",
                       "img": "1.jpg",
                       "pid": 1,
                       "price": 200,
                       "is_delete": 0,
                       "children": [
                           {
                               "id": 11,
                               "create_time": "2020-06-03T11:26:56",
                               "name": "Python初级1",
                               "desc": "Python初级1",
                               "category": "会员",
                               "img": "1.jpg",
                               "pid": 2,
                               "price": 100,
                               "is_delete": 0
                           }
                       ]
                   },
                   {
                       "id": 3,
                       "create_time": "2020-06-03T09:35:08",
                       "name": "Python中级",
                       "desc": "Python中级",
                       "category": "会员",
                       "img": "1.jpg",
                       "pid": 1,
                       "price": 200,
                       "is_delete": 0
                   },
                   {
                       "id": 4,
                       "create_time": "2020-06-03T09:35:12",
                       "name": "Python高级",
                       "desc": "Python高级",
                       "category": "会员",
                       "img": "1.jpg",
                       "pid": 1,
                       "price": 200,
                       "is_delete": 0
                   }
               ]
           },
           {
               "id": 5,
               "create_time": "2020-06-03T09:35:48",
               "name": "Java",
               "desc": "Java全套带回家",
               "category": "限免",
               "img": "2.jpg",
               "pid": 0,
               "price": 400,
               "is_delete": 0,
               "children": [
                   {
                       "id": 6,
                       "create_time": "2020-06-03T09:36:20",
                       "name": "Java初级",
                       "desc": "Java初级",
                       "category": "限免",
                       "img": "2.jpg",
                       "pid": 5,
                       "price": 100,
                       "is_delete": 0
                   },
                   {
                       "id": 7,
                       "create_time": "2020-06-03T09:57:06",
                       "name": "Java中级",
                       "desc": "Java中级",
                       "category": "限免",
                       "img": "2.jpg",
                       "pid": 5,
                       "price": 100,
                       "is_delete": 0
                   },
                   {
                       "id": 8,
                       "create_time": "2020-06-03T09:57:13",
                       "name": "Java高级",
                       "desc": "Java高级",
                       "category": "限免",
                       "img": "2.jpg",
                       "pid": 5,
                       "price": 100,
                       "is_delete": 0
                   }
               ]
           },
           {
               "id": 10,
               "create_time": "2020-06-03T11:17:34",
               "name": "Ubuntu",
               "desc": "Ubuntu全套带回家",
               "category": "会员",
               "img": "3.jpg",
               "pid": 0,
               "price": 300,
               "is_delete": 0
           }
       ]
   }
   ```

7. 接口没问题，接下来就要在前端实现效果了，我们使用vue递归组件实现效果

   所谓递归组件: 就是组件可以在它们自己的模板中调用自身，不过它们只能通过 name 选项来做这件事，例如给组件设置属性 name: 'Reply'，然后在模板中就可以使用 Reply 调用自己进行递归调用了

   

   ```vue
   // Reply.vue 这是个组件
   <template>
       <div>
           // 由于数据太多，只显示个名称就可以看到效果了
           <div :class="[data.id==0 ? 'root': '', 'reply']">{{ data.name }}
           <li >
               <ul v-if="data.children && data.children.length>0">
                   <Reply v-for="child in data.children" :key="child.id" :data="child"/>
               </ul>
           </li>
           </div>
       </div>
   </template>
       
       <script>
       export default {
           name: 'Reply', // 递归组件需要设置 name 属性，才能在模板中调用自己
           props:['data'],
       };
       </script>
       
       <style >
       .reply {
           padding-left: 4px;
           border-left: 1px solid #eee;
       }
   
       ul {
               padding-left: 20px;
               list-style: none;
           }
   
           .root { display: none; }
   
       
   
       </style>
   ```

8. 最后在其他任意组件中调用Reply.vue组件，传入数据即可

   ```vue
   <template>
     <div>
           <div>
               <ul>    
                   <span v-for="i in datas">
                     <Reply  :data="i" />
                   </span>
               </ul>
           </div>
     </div>
   </template>
   
   <script>
   import Reply from './comm/Reply.vue';
   // 我这里的axios接口进行过封装，可以直接调用，这样可以省去大量axios请求接口
   import { courselist_get } from './axios_api/api'
   export default {
       data(){
           return{ 
             datas:{},
             online: 0
           }
       },
       components:{
           'Reply':Reply,
       },
       created(){
           this.load()
       },
       methods:{
           load(){
         courselist_get().then(res=>{
           if(res.code == 200){
             this.datas = res.data
           }else {
             
           }
         })
         
     }
       }
   }
   </script>
   
   <style>
   
   </style>
   ```

9. 调用接口，查看效果

   ![gitee](https://wangxs020202.gitee.io/images/note/cexiao.png)

   

### 总结

使用vue递归组件我们需要注意：

1. 组件必须含有 name 这个属性,因为没有 name 这个属性会造成控件自身不能调用自身,自身调用的时候最好有绑定 key ,因为这个 key 是唯一的标识,对于 vue   更新控件比较好.除非控件非常简单就不用 key.
2. 另外一个需要注意就是递归组件时候,需要有一个条件来终止递归,在这里使用 v-for 隐形条件终止递归. props 这个属性其实主要传递父控件的数据的参数。

