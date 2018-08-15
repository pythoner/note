最近再读ffmpeg的源码， 其中有一段源码如下：
```
int av_reallocp(void *ptr, size_t size)
{
    void *val;

    if (!size) {
        av_freep(ptr);
        return 0;
    }
   // void *memcpy(void *dest, const void *src, size_t n);
   //    The  memcpy()  function copies n bytes from memory area src to memory area dest.
   // 为什么这里的第一个参数要用&val, 而不是val？
    memcpy(&val, ptr, sizeof(val));
    val = av_realloc(val, size);

    if (!val) {
        av_freep(ptr);
        return AVERROR(ENOMEM);
    }

    memcpy(ptr, &val, sizeof(val));
    return 0;
}
```

对于上面的程序， 关于memcpy的部分， 这里使用
```
memcpy(&val, ptr, sizeof(val));
```
为什么不可以使用
```
memcpy(val, ptr, sizeof(val));
```


我们简单的使用一个例子来说明一下：
```
//file: pointer1.c
#include <stdio.h>
#include <string.h>

int main(){
   void *p;
   long a = 10;
   printf("%d\n", sizeof(a));
   printf("%d\n", sizeof(p));

   memcpy(p, &a, sizeof(p));
}          
```
编译、执行
 ```
[lee@lee c-code]$ gcc ./pointer1.c
[lee@lee c-code]$ ./a.out
8
8
Segmentation fault (core dumped)
 ```
这里会报一个段错误。


我们在用&p， 测试一下
```
//file: pointer2.c
#include <stdio.h>
#include <string.h>

int main(){
   void *p;
   long a = 10;
   printf("%d\n", sizeof(a));
   printf("%d\n", sizeof(p));

   memcpy(&p, &a, sizeof(p));
}          
```
编译执行
```
[lee@lee c-code]$ gcc pointer2.c
[lee@lee c-code]$ ./a.out
8
8

```
程序可以正常运行。

让我们看一下**void *p**中p的值是什么
```
#file: pointer3.c
#include <stdio.h>

int main(){
   void *p;
   printf("%d\n", sizeof(p));
   printf("-------------------\n");
   int i = 0;
   for(; i < sizeof(p); i++){
     printf("%x\n", p);
     p += 1;
   }
}
~  
```
编译、执行
```
[lee@lee c-code]$ gcc ./pointer3.c
[lee@lee c-code]$ ./a.out
8
-------------------
0
1
2
3
4
5
6
7
```

可以看到我们这里定义的空指针p, 执行的内存0开始的8个字节。 对于0号内存地址，对于我们来说， 是不可访问的。
所以报出来的上面的*Segmentation fault*。


* 空指针
  没有存储任何内存的指针就称之位空指针。空指针就是没有被具体初始化之前， 其值位0的指针。也就是说， 空指针都是指向0号地址。


#### 空指针参考
https://blog.csdn.net/shuimanting520/article/details/51360139
https://blog.csdn.net/somewhere117/article/details/4430871
https://blog.csdn.net/bailyzheng/article/details/7677628
https://www.cnblogs.com/x_wukong/p/5664916.html
https://blog.csdn.net/hustyangju/article/details/25691165
