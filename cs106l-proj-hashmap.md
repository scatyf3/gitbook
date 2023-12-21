---
description: 哈希表，求职简历必备项目
---

# CS106L-proj-HashMap

这个项目很好，很推荐大家做，原因如下：

* 可以加深对现代c++的理解，加深项目能力
* tips很充分，几乎是手把手带你写
* 任务量不多，感觉甚至从头开始看对应ppt+写代码，最多一天就能搞定
* 测试用例详细且为本地用例

## 能改善生活质量的tips

### build.sh

在测试用例里会产生一些warning，通过更改build.sh的参数，可以关掉这些warning：

```shellscript
g++ -Wall -Werror -std=c++17 main.cpp -o main -Wno-self-move  -Wno-self-assign-overloaded -Wno-unused-but-set-variable
```

但有一说一，不太确定这种修改是不是在这个作业里是合法的，我可以确定的是`-Wself-move`  是GCC13，即2023版本的编译器新增的功能（[https://developers.redhat.com/articles/2023/06/21/new-c-features-gcc-13](https://developers.redhat.com/articles/2023/06/21/new-c-features-gcc-13)），大约会是22冬作业未考虑到的，但是另外两个warning并不是新功能

### 关于恒常正确性



