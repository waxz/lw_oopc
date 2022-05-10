增加对没有MMU的嵌入式设备的支持
=========================

用[tinyalloc](https://github.com/thi-ng/tinyalloc)替换C标准库`malloc.h`中的`malloc`。


## `tinyalloc` 用法
```c
#include "tinyalloc.h"

#define BUFFER_SIZE 10000
char BUFFER[BUFFER_SIZE];

int main()
{
    ta_init(BUFFER,BUFFER+BUFFER_SIZE,256,16,16);
    int*ptr = ta_alloc(12);
    ta_free(ptr);
    return 0;
}
```

## LW_OOPC 用法
1. 在`lw_oopc.h`中设置`malloc`
```c
// 是否使用malloc
//#define LW_OOPC_USE_STDLIB_MALLOC     // 表示使用C标准定义的 malloc
#define LW_OOPC_USE_USER_DEFINED_MALLOC // 表示使用用户自定义的 tinyalloc
```
2. 如果使用`tinyalloc`需要在`ta_init`之后进行对象创建。
```c
int main()
{
    ta_init(BUFFER,BUFFER+BUFFER_SIZE,256,16,16);
    int*ptr = ta_alloc(12);
    ta_free(ptr);
    
    Fish* fish = Fish_new();    // 创建鱼对象
    Dog* dog = Dog_new();       // 创建狗对象
    Car* car = Car_new();       // 创建车子对象
    lw_oopc_delete(fish);
    lw_oopc_delete(dog);
    lw_oopc_delete(car);

    return 0;
}
```



LW_OOPC 升级版本发布说明
=========================

本人在金永华对于高焕堂的 lw_oopc 的源码的基础上进行了一些简单的修改和调试。使得 lw_oopc 能够用在实际的嵌入式项目中，作为一个很好的框架传播开来。

主要改进有：
1. 修正了一些小错误，使得编译通过。
2. 调整了一些宏的位置，使得配置接口只在 lw_oopc.h 中。
3. 经过测试可以直接在*nix 下编译运行，删掉多余的 VS 工程文件。

我的联系方式：
* 博客: <https://akjong.com>
* E-mail: <akagi201@gmail.com>

## 以下为金永华 (loveyfore@sohu.com) 在他的 repo 上的一些说明。

* 在高焕堂先生的著作[《UML+OOPC 嵌入式 C 语言开发精讲》](doc/UML+OOPC嵌入式C语言开发精讲.pdf)中介绍了高先生及其 MISOO 团队创作的 lw_oopc 宏。
* 在研读这套宏的过程中，我发现高先生提供的这套宏还过于简陋，不能够优雅地支持面向接口编程。
* 经过认真钻研，我对原有的这套宏进行了扩充和改良，得到了 lw_oopc 宏的升级改进版本。
* 改良后的这套宏，能够很好地支持面向对象编程，能够支持面向接口编程 (甚至支持复杂的多重继承).

- 经邮件与高焕堂先生联系，征得高先生同意，以 LGPL 协议开源，感谢高先生以及 MISOO 团队的贡献！


### 原有高焕堂先生及其 MISOO 团队创作的宏 (总共 6 个宏)，清单如下：

高焕堂及 MISOO 创作的宏 | 是否存在问题？| 是否修改？
---- | ---- | ----- 
INTERFACE | 没有问题 | 否
CLASS | 无法支持继承 | 是
CTOR | 对申请不到内存的情况未保护 | 是
END_CTOR | 没有问题 | 否
FUNCTION_SETTING | 没有问题 | 否
IMPLEMENTS | 没有问题 | 否

### 为了更好的支持面向对象以及面向接口编程，金永华增加了 14 个宏：

创作的宏 | 创作目的 () 为了解决什么问题？)
---- | ----
DTOR / END_DTOR | 为了支持析构函数的概念
ABS_CLASS | 为了支持抽象类的概念
ABS_CTOR / END_ABS_CTOR | 为了支持可继承的抽象类的构造函数
EXTENDS | 为了让熟悉 Java 的人容易理解 (与 IMPLEMENTS 宏等同)
SUPER_CTOR | 为了支持子类调用父类的构造函数
SUPER_PTR / SUPER_PTR_2 / SUPER_PTR_3 | 为了支持向上转型
SUB_PTR / SUB_PTR_2 / SUB_PTR_3 | 为了支持向下转型
INHERIT_FROM | 为了支持访问直接父类的数据成员

* 希望这套宏，能够真正帮助到想用 C 语言写出面向对象代码的 C 程序员们！
