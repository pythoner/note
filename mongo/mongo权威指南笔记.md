mongodb都有特性：
1. 固定集合
   集合的大小是有上限的。这对某些类型的数据（比如日志）特别有用

 > 固定集合， 如果超过大小的上限怎么办？ 创建一个新的，还是删除旧的？

2. 文件存储
   mongodb支持用一种容易使用的协议存储大型文件和文件的元数据
3. 存储javascript


缺点：
   1. 表的关联join。
   2. 多行事物





##### 文档是有顺序的
```
{"greeting":"hello world", "foo":3}
```
和
```
{"foo":3, "greeting": "hello world"}
```
是两个文档


#### 文档的键是字符串，除了少数例外情况， 键可以使用任意的UTF-8字符

 * 键不能含有\0(空字符)， 这个字符表示键的结尾
空字符是ASCII为0的对应的字符。 在C语言中用NULL来表示
 ```
 > db.tur.insert({"greeting you\0":"hello world", "foo":1})
2018-08-30T11:01:48.387+0800 Error: JavaScript property (name) contains a null char which is not allowed in BSON. {} at src/mongo/shell/bulk_api.js:602

> db.tur.find()
> db.tur.insert({"greeting you": "hello world", "foo": 1})
WriteResult({ "nInserted" : 1 })
> db.tur.find()
{ "_id" : ObjectId("5b875ed0da19a8e7a332a3a4"), "greeting you" : "hello world", "foo" : 1 }
 ```
 > 这里的空字符，并不是空格字符。 空格对应的ASCII为32
```
In [22]: ord(" ")
Out[22]: 32
```

* **.** 和**$**由特殊的意义， 只有在特定的情况下才能使用。一般不太建议在key中使用
```
> db.tur.insert({"greeting.you": "hello world", "foo":2})
2018-08-30T11:08:10.895+0800 can't have . in field names [greeting.you] at src/mongo/shell/collection.js:155

> db.tur.insert({"greeting$you": "hello world", "foo":3})
WriteResult({ "nInserted" : 1 })
>
> db.tur.insert({"greeting$you": "hello world", "foo":3})
WriteResult({ "nInserted" : 1 })
>
> db.tur.find()
{ "_id" : ObjectId("5b875ed0da19a8e7a332a3a4"), "greeting you" : "hello world", "foo" : 1 }
{ "_id" : ObjectId("5b875fbcda19a8e7a332a3a5"), "greeting$you" : "hello world", "foo" : 3 }
{ "_id" : ObjectId("5b875fbeda19a8e7a332a3a6"), "greeting$you" : "hello world", "foo" : 3

```
如何界定是特殊情况， 如何才不可以使用？？？

* 以下划线**_**开头的键是保留的， 虽然这个并不是严格要求的。 如**_id**



#### 集合

* 无模式
集合是**无模式**的， 这意味着一个集合里面的文档可以是各式各样的。 下面两个文档可以存在于同一个集合里面
```
> db.tur.find()
> db.tur.insert({"greeting":"hello world"})
WriteResult({ "nInserted" : 1 })
> db.tur.insert({"foo": 3})
WriteResult({ "nInserted" : 1 })
> db.tur.find()
{ "_id" : ObjectId("5b87622eda19a8e7a332a3a7"), "greeting" : "hello world" }
{ "_id" : ObjectId("5b876237da19a8e7a332a3a8"), "foo" : 3 }
```

#### 数据库

数据库名最终会变成文件系统里的文件

有一些数据库的名是保留的， 可以直接访问这些由特殊作用的数据库
* admin
从权限的角度来讲， 这是"root"数据库，。要是将一个用户添加到这个数据库， 这个用户自动继承所有数据库的权限。
一些特定的服务端命令也只能从这个数据库运行，比如列出所有的数据库和关闭服务器。
> 这里的列出所有的数据库命令是什么？

* local
这个数据永远不会被复制， 可以用来存储限于本地单台服务器的任意集合

* config
当mongo用于分片设置时， config数据库在内部使用，用于保存分片的相关信息。

把数据库的名字放到集合名前面， 得到的就是集合的完全限定名， 成为命名空间。如， 如果你在cms数据库
中使用blog.posts集合， 那么这个集合的命名空间就是cms.blog.posts.


#### 启动mongodb

插入原理:  原理注入式攻击

当执行插入的时候， 使用的驱动程序会将数据转换为BSON的形式，然后将其送入数据库。数据库解析BSON， 校验
是否包含"\_id"键并且文档不超过4MB， 除此之外， 不做别的数据验证， 就是简单的将文档原样的存入数据库中。
这会带来些或好或坏的影响，最明显的副作用就是允许插入无效的数据，而从好处看，它能让数据更加安全，
远离注入式攻击。

mongodb在插入时并不执行代码， 所以这块没有注入攻击的可能。


* 删除

 db.collection.remove 不会删除集合本身， 原有的索引也会保留
 db.collection.drop 会删除集合本身， 然后重建索引

如果要**删除集合中的所有数据**， 建议还是用drop， 因为drop比remove要快很多。


*  更新文档

文档存入数据库以后，可以使用update方法来更新它。 update有两个参数， 一个时查询器， 另一个时修改器。
**更新操作是原子的， 若是两个更新同时发生， 先到达服务器的先执行， 接着执行另外一个， 所以，
互相有冲突的更新可以火速传递， 并不会相互干扰，最后的更新会取的胜利**


- $set
"$set" 用来指定一个键的值， 如果这个键不存在，则创建它。
"$unset"可以用于删除一个键
 ```
 > db.games.find()
>
>
> db.games.insert({"game":"pinball", "user":"joe"})
WriteResult({ "nInserted" : 1 })
> db.games.find().pretty()
{
	"_id" : ObjectId("5b88d7116d3128dd710f346b"),
	"game" : "pinball",
	"user" : "joe"
}
> db.games.update({"user":"joe"}, {"$set":{"score": 50}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.games.find().pretty()
{
	"_id" : ObjectId("5b88d7116d3128dd710f346b"),
	"game" : "pinball",
	"user" : "joe",
	"score" : 50
}
> db.games.update({"user":"joe"}, {"$inc": {"score": 50}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.games.find().pretty()
{
	"_id" : ObjectId("5b88d7116d3128dd710f346b"),
	"game" : "pinball",
	"user" : "joe",
	"score" : 100
}
> db.games.update({"user":"joe"}, {"$unset": {"score": 1}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.games.find().pretty()
{
	"_id" : ObjectId("5b88d7116d3128dd710f346b"),
	"game" : "pinball",
	"user" : "joe"
}

 ```
"$inc" 修改器用来增加已有键的值， 或者在键不存在的时候创建一个键

**增加， 修改，删除键的时候， 应该使用$修改器**。 要把“foo”设置成“bar”， 常见的错误做法如下：
```
> db.coll.update(criteria, {"foo": "bar"})
```
这会事与愿违， 实际上这会将整个文档用{"foo", "bar"}替换掉，
```
> db.games.find()
{ "_id" : ObjectId("5b88d7116d3128dd710f346b"), "game" : "pinball", "user" : "joe" }
>
> db.games.update({"user":"joe"}, {"user":"sam"})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
>
> db.games.find()
{ "_id" : ObjectId("5b88d7116d3128dd710f346b"), "user" : "sam" }
>
```

数组修改器。 如果指定的键已经存在， “$push” 会向已有的数组末尾加入一个元素， 要是没有就会创建一个新的数组。
````
> db.blog.posts.find().pretty()
{
	"_id" : ObjectId("5b8c97626d3128dd710f346c"),
	"title" : "A blog post",
	"content" : "..."
}
> db.blog.posts.update({"title":"A blog post"}, {"$push": {"comments": {"name":"joe@example.com", "content":"nice post."}}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.blog.posts.find().pretty()
{
	"_id" : ObjectId("5b8c97626d3128dd710f346c"),
	"title" : "A blog post",
	"content" : "...",
	"comments" : [
		{
			"name" : "joe@example.com",
			"content" : "nice post."
		}

```
经常会有这种情况， 如果一个值不再数组里面就把它添加进去。 可以在查询文档中用"$ne"来实现。



"$ne"和"$addToSet"可以完成同样的功能， 但是有些时候适用于"$ne", 有些时候使用于"$addToSet",
例如： 有一个表示用户的文档， 已经有了电子邮件的地址
```
> db.users.find().pretty()
{
	"_id" : ObjectId("5b8c9d496d3128dd710f346d"),
	"username" : "joe",
	"emails" : [
		"joe@example.com",
		"joe@gmail.com",
		"joe@yahoo.com"
	]
}
>
>
>
> db.users.update({"_id": ObjectId("5b8c9d496d3128dd710f346d")}, {"$addtoset":{"emails":"joe@gmail.com"}})
WriteResult({
	"nMatched" : 0,
	"nUpserted" : 0,
	"nModified" : 0,
	"writeError" : {
		"code" : 9,
		"errmsg" : "Unknown modifier: $addtoset"
	}
})
>
> db.users.update({"_id": ObjectId("5b8c9d496d3128dd710f346d")}, {"$addToSet":{"emails":"joe@gmail.com"}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 0 })
>
>
> db.users.find().pretty()
{
	"_id" : ObjectId("5b8c9d496d3128dd710f346d"),
	"username" : "joe",
	"emails" : [
		"joe@example.com",
		"joe@gmail.com",
		"joe@yahoo.com"
	]
}
```
**数组操作： 当添加新的地址时， 用"$addToSet"可以避免重复**：
```
> db.users.update({"_id": ObjectId("5b8c9d496d3128dd710f346d")}, {"$addToSet":{"emails":"joe@hotmail.com"}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
>
> db.users.find().pretty()
{
	"_id" : ObjectId("5b8c9d496d3128dd710f346d"),
	"username" : "joe",
	"emails" : [
		"joe@example.com",
		"joe@gmail.com",
		"joe@yahoo.com",
		"joe@hotmail.com"
	]
}
```

**数组操作： 有几个从数组中删除元素的方法， 若是把数组堪称队列或者栈， 可以使用"$pop", 这个修改器可以从数组的任何一端删除元素。 {$pop: {key:1}} 从数组的末尾删除一个元素。 {$pop: {key: -1}}则从头部删除**

**数组操作： 有时候需要基于特定的条件来删除元素，而不仅仅是依据为值， "$pull", 可以做到**。 例如， 有个带完成事项列表，顺序有些问题：
```
> db.lists.find().pretty()
{
	"_id" : ObjectId("5b8ca1d16d3128dd710f346e"),
	"todo" : [
		"dishes",
		"laundry",
		"dry cleaning"
	]
}
> db.lists.update({}, {"$pull": {"todo": "laundry"}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
>
> db.lists.find().pretty()
{
	"_id" : ObjectId("5b8ca1d16d3128dd710f346e"),
	"todo" : [
		"dishes",
		"dry cleaning"
	]

```
**数组操作： $pull会将所有匹配到的部分删掉， 对数组[1, 1, 2, 1]执行pull 1, 得到的结果就是只有一个元素的数组[2]**


#### 数组的定位修改器
如果数组有多个值， 而我们只想对其中的一部分进行操作， 这就需要一些技巧， **有两种方法操作数组中的值： 通过位置或者定位操作符（"$"）**

```
> db.blog.posts.find({"comments.name": "joe@example.com"}).pretty()
{
	"_id" : ObjectId("5b8c97626d3128dd710f346c"),
	"title" : "A blog post",
	"content" : "...",
	"comments" : [
		{
			"name" : "joe@example.com",
			"content" : "nice post."
		},
		{
			"name" : "bob@example.com",
			"content" : "good post."
		},
		{
			"name" : "sam@example.com",
			"content" : "I thought it was too short"
		}
	]
}
>
> db.blog.posts.update({"comments.name": "joe@example.com"}, {"$set": {"comments.$.votes": 1}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
>
>
>
> db.blog.posts.find().pretty()
{
	"_id" : ObjectId("5b8c97626d3128dd710f346c"),
	"title" : "A blog post",
	"content" : "...",
	"comments" : [
		{
			"name" : "joe@example.com",
			"content" : "nice post.",
			"votes" : 1
		},
		{
			"name" : "bob@example.com",
			"content" : "good post."
		},
		{
			"name" : "sam@example.com",
			"content" : "I thought it was too short"
		}
	]
}
> db.blog.posts.update({"comments.name": "bob@example.com"}, {"$set": {"comments.$.votes": 2}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.blog.posts.update({"comments.name": "sam@example.com"}, {"$set": {"comments.$.votes": 3}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
>
>
>
> db.blog.posts.find().pretty()
{
	"_id" : ObjectId("5b8c97626d3128dd710f346c"),
	"title" : "A blog post",
	"content" : "...",
	"comments" : [
		{
			"name" : "joe@example.com",
			"content" : "nice post.",
			"votes" : 1
		},
		{
			"name" : "bob@example.com",
			"content" : "good post.",
			"votes" : 2
		},
		{
			"name" : "sam@example.com",
			"content" : "I thought it was too short",
			"votes" : 3
		}
	]
}

```
**Mongodb提供了定位操作符"$", 用来定位查询文档已经匹配的元素， 并进行更新。例如上例中，我们分别将votes改为了0， 定位符只更新第一个匹配到的元素**
> 如何通过一条语句，将数组中所有元素的votes改为0/为数组中的所有元素添加一个新的votes

定位操作符"$"的另外一个在查询中的作用
```
> db.blog.posts.find().pretty()
{
	"_id" : ObjectId("5b8c97626d3128dd710f346c"),
	"title" : "A blog post",
	"content" : "...",
	"comments" : [
		{
			"name" : "joe@example.com",
			"content" : "nice post.",
			"votes" : 1
		},
		{
			"name" : "bob@example.com",
			"content" : "good post.",
			"votes" : 2
		},
		{
			"name" : "sam@example.com",
			"content" : "I thought it was too short",
			"votes" : 3
		}
	]
}
> db.blog.posts.find({"comments.name":{"$eq": "bob@example.com"}}, {"title":1, "content":1, "_id":0, "comments.$":1}).pretty()
{
	"title" : "A blog post",
	"content" : "...",
	"comments" : [
		{
			"name" : "bob@example.com",
			"content" : "good post.",
			"votes" : 2
		}
	]
}

```
#### 数组查询

数组绝大多数情况下可以这样理解： **数组中每一个元素都是整个键的值**， 例如， 如果数组是一个水果清单， 如下所示：
```
> db.food.insert({"fruit":["apple", "banana", "peach"]})
WriteResult({ "nInserted" : 1 })

> db.food.find().pretty()
{
	"_id" : ObjectId("5b8ce1996d3128dd710f346f"),
	"fruit" : [
		"apple",
		"banana",
		"peach"
	]
}
```

下面的查询会匹配该文档
```
> db.food.find({"fruit":"banana"}).pretty()
{
	"_id" : ObjectId("5b8ce1996d3128dd710f346f"),
	"fruit" : [
		"apple",
		"banana",
		"peach"
	]
}
```

* **$all**

 如果需要通过多个元素来匹配数组， 就要用"$all". 这样就会匹配一组数据
 ```
 > db.food.find()
{ "_id" : ObjectId("5b8ce1996d3128dd710f346f"), "fruit" : [ "apple", "banana", "peach" ] }
{ "_id" : ObjectId("5b8ce4ca6d3128dd710f3470"), "fruit" : [ "apple", "kumquat", "orange" ] }
{ "_id" : ObjectId("5b8ce4e26d3128dd710f3471"), "fruit" : [ "cherry", "banana", "apple" ] }
 ```

要找到即有"apple"，又有“banana”的文档， 就的用"$all" 来查询。
```
> db.food.find({"fruit": {$all: ["banana", "apple"]}})
{ "_id" : ObjectId("5b8ce1996d3128dd710f346f"), "fruit" : [ "apple", "banana", "peach" ] }
{ "_id" : ObjectId("5b8ce4e26d3128dd710f3471"), "fruit" : [ "cherry", "banana", "apple" ] }
```
顺序无关紧要。

如果想查询数据指定位置的元素， 则需要使用key.index语法指定下表，
```
> db.food.find({"fruit.2": "apple"}).pretty()
{
	"_id" : ObjectId("5b8ce4e26d3128dd710f3471"),
	"fruit" : [
		"cherry",
		"banana",
		"apple"
	]
}
```
> 数组下标都是从0开始的。

## mongodb 权威指南关于$size部分不对， 需要在仔细看看

* $size
用于查询指定长度的数组

> 注意这里如果update没有给第四个参数的话， 那么， 仅仅是更新第一个
```
> db.food.update({}, {"$push": {"fruit": "strawberry"}})
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
>
>
> db.food.find().pretty()
{
	"_id" : ObjectId("5b8ce1996d3128dd710f346f"),
	"fruit" : [
		"apple",
		"banana",
		"peach",
		"strawberry"
	]
}
{
	"_id" : ObjectId("5b8ce4ca6d3128dd710f3470"),
	"fruit" : [
		"apple",
		"kumquat",
		"orange"
	]
}
{
	"_id" : ObjectId("5b8ce4e26d3128dd710f3471"),
	"fruit" : [
		"cherry",
		"banana",
		"apple"
	]
}

同时更新多个
> db.food.update({}, {"$push": {"fruit": "strawberry"}}, false, true)
WriteResult({ "nMatched" : 3, "nUpserted" : 0, "nModified" : 3 })
>
> db.food.find().pretty()
{
	"_id" : ObjectId("5b8ce1996d3128dd710f346f"),
	"fruit" : [
		"apple",
		"banana",
		"peach",
		"strawberry",
		"strawberry"
	]
}
{
	"_id" : ObjectId("5b8ce4ca6d3128dd710f3470"),
	"fruit" : [
		"apple",
		"kumquat",
		"orange",
		"strawberry"
	]
}
{
	"_id" : ObjectId("5b8ce4e26d3128dd710f3471"),
	"fruit" : [
		"cherry",
		"banana",
		"apple",
		"strawberry"
	]
}

```


* $slice操作符
返回数组的一个子集合

例如， 返回一个博客文档的前2条评论
```
> db.blog.posts.find().pretty()
{
	"_id" : ObjectId("5b8c97626d3128dd710f346c"),
	"title" : "A blog post",
	"content" : "...",
	"comments" : [
		{
			"name" : "joe@example.com",
			"content" : "nice post.",
			"votes" : 1
		},
		{
			"name" : "bob@example.com",
			"content" : "good post.",
			"votes" : 2
		},
		{
			"name" : "sam@example.com",
			"content" : "I thought it was too short",
			"votes" : 3
		}
	]
}

返回前两条评论
> db.blog.posts.find({}, {"comments":{"$slice": 2}}).pretty()
{
	"_id" : ObjectId("5b8c97626d3128dd710f346c"),
	"title" : "A blog post",
	"content" : "...",
	"comments" : [
		{
			"name" : "joe@example.com",
			"content" : "nice post.",
			"votes" : 1
		},
		{
			"name" : "bob@example.com",
			"content" : "good post.",
			"votes" : 2
		}
	]
}
```

如果想要返回后10条，只要用-10就可以了.

下例中我们返回组后一条评论
```
> db.blog.posts.find({}, {"comments": {"$slice":-1}}).pretty()
{
	"_id" : ObjectId("5b8c97626d3128dd710f346c"),
	"title" : "A blog post",
	"content" : "...",
	"comments" : [
		{
			"name" : "sam@example.com",
			"content" : "I thought it was too short",
			"votes" : 3
		}
	]
}

```

$slice 也接受偏移值和要返回的元素数量。例如:
```
db.blog.posts.find(criteria, {"comments": {"$slice": [23, 10]}})
```
这个操作会跳过前23个元素， 返回第24个～第33个元素。


###  4.5.4 获得一致结果
这里说： **如果文档体积增加而预留空间不足**, 就需要将其移动

问：
文档的预留空间是多少？文档体积最大是多少？


### 索引

* 创建索引使用*ensureIndex*方法
1>  前置索引
2>  创建索引的缺点就是每次插入、更新和删除都会产生额外的开销， 这是因为数据库不但需要执行这些操作， 还要将这些操作在集合的索引中标记。因此，要尽可能少创建 索引。

## 5.1.1 扩展索引

假设我们有个集合， 保存了用户的状态信息。 现在想要查询用户和日期， 取出某一个用户最近的状态。以我们目前所学， 我们回像下面这样创建一个索引
```
db.stats.ensureIndex({"user":1, date: -1})
```
这会使用户和日期的查询非常快，但是并不是最好的方式.
首先想一想我们目前的索引, 按照用户名(生序)排序， 而后按照日期（降序）排序，所以会是这种形式：
```
User 123 On Match 13, 2010
User 123 On Match 12, 2010
User 123 On Match 11, 2010
User 123 On Match 5, 2010
User 123 On Match 4, 2010
User 124 On Match 12, 2010
User 124 On Match 11, 2010
```
 这点数据还行， 如果应用会有数百万的用户， 每人每天有数十条状态更新，若是每条用户状态的索引值占用类似一页值的磁盘空间， 那么对于每次“最新状态”的查询， 数据库都会将不同的页载入内存。若是站点太热门， **内存放不下所有的索引， 就会非常非常满**。
  
