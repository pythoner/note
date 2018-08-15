指针占用的内存空间的大小与指针执行的内容所占的空间大小无关。
在不同的操作系统以及编译环境下， 指针类型占用的字节数是不同的。 一般而言：

* 编译生成的16位代码时， 指针占2个字节
* 编译生成的32位代码时， 指针占4个字节
* 编译生成的64位代码时， 指针占8个字节

举例如下
```
//filename: address_size.c

#include <stdio.h>

int main(){
  void* p;
  printf("%d\n", sizeof(p));
}
```

```
[lee@lee c-code]$ gcc ./address_size.c
[lee@lee c-code]$
[lee@lee c-code]$
[lee@lee c-code]$ ./a.out
8
```
