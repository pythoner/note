
今天在社区看到这么一道题，问题描述如下：

设两个字符串中所含有的字符和个数都相同我们就叫这两个字符串匹配，比如：abcda和adabc,由于出现的字符个数都是相同，只是顺序不同，所以这两个字符串是匹配的。要求高效！

当然考虑用高效快捷的Python来练一练，看到有人很快就写下了以下代码：

<pre>
>>> sorted("abcda") == sorted("adabc")
True
</pre>


也有人是这样写的, **使用2.7 里面新增的Counter类**
<pre>
>>> from collections import Counter
>>> Counter("abcda") == Counter("adabc")
True
</pre>

看看结果是一样的
<pre>
>>> Counter("abcda")
Counter({'a': 2, 'c': 1, 'b': 1, 'd': 1})
>>> Counter("adabc")
Counter({'a': 2, 'c': 1, 'b': 1, 'd': 1})
</pre>

看到人家的第二种解法， 想起来了一到面试题， 统计一个字符串中每个字符出现的个数。这里假定字符串是“abcddafjasdjljnleadfala”
按照我以前的逻辑是这样写的.
<pre>
>>>	s = 'abcddafjasdjljnleadfala'
>>>	d = {}
>>>	for i in s:
    	if i in d:
    	    d[i] = d[i] + 1
    	else:
    	    d[i] = 1
        
>>>	print d

#如今发现Counter就是专门做这个事情的， 直接调用Counter就OK了。
</pre>

********************
>http://www.ituring.com.cn/article/11707?q=python


















