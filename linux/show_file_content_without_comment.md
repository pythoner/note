###linux系统中配置文件出去注释行的方法

linux命令
<pre>
$ grep -v "^#" filename
</pre>

说明：

+ -v 就是取反
+ ^# 表示以#开头的行

这个命令的意思就是显示filename中不以"#"开头的行


***
---
___
[参考](http://c.pass.blog.163.com/blog/static/118139638201051813237567/)
