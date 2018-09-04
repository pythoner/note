
```
[lee@lee FlameGraph]$ cat /home/lee/PycharmProjects/tttt/opencv_image_read_performance.py
#!/usr/bin/env python
# encoding: utf-8

"""
@author: lee
@software: PyCharm Community Edition
@file: opencv_image_read_performance.py
@time: 7/27/18 11:21 AM
"""

import cv2
import time
import os
import sys

path = "/home/lee/8k/lossless"


def do():
    # if cv2.useOptimized():
    #     cv2.setUseOptimized(False)
    read_ticks = []
    # if not os.path.isdir(path):
    #     sys.exit(1)
    files = os.listdir(path)
    for item in files*10:
        filename = os.path.join(path, item)
        if os.path.isdir(filename):
            continue
        t1 = time.time()
        image = cv2.imread(filename)
        read_ticks.append(time.time() - t1)
        # print image
        # cv2.imshow("demo", image)
        if cv2.waitKey(1) & 0xFF == ord("q"):
            return
    return read_ticks

if __name__ == "__main__":
    read_ticks = do()
    print read_ticks
    print '**'*20
    print sum(read_ticks)/len(read_ticks)
    cv2.destroyAllWindows()
```

1. perf 采集数据
```
[lee@lee FlameGraph]$ sudo perf record -F 99 -a -g python /home/lee/PycharmProjects/tttt/opencv_image_read_performance.py
```

2. 用 *perf script* 工具对 *perf.data* 进行解析
```
[lee@lee FlameGraph]$ sudo perf script -i perf.data > perf.unfold
```

3. 用 *stackcollapse-perf.pl* 将 *perf* 解析出的内容 *perf.unfold* 中的符号进行折叠
```
[lee@lee FlameGraph]$ sudo ./stackcollapse-perf.pl perf.unfold > perf.folded

```
4. 生成svg图
```
[lee@lee FlameGraph]$ sudo ./flamegraph.pl perf.folded > read_perf.svg
```



##### 参考
https://blog.csdn.net/gatieme/article/details/78885908
