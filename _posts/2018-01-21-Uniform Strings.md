---
title: Uniform Strings
date: 2018-01-21 17:00:00
---

## 问题描述

给定一个长度为8的字符串，这个字符串只含有0或1，并且这个字符串采用的是循环的模式。你需要在对字符串进行一次遍历时，找到0-1或1-0转换的数量。从任何一个字母开始，一直循环，直到你回到相同的字母，并找到其中的转换的数量。如果最多只有两个这样的转换，则这个字符串被称为 _uniform_，否则被称为 _non-uniform_。

给定字符串s，说明这个字符串是否是 _uniform_。

## 输入

输入的第一行是一个整数T，表示测试情况的数量，每个测试情况如下：只有一行的输入，为字符串s。

## 输出

对于每种测试情况，如果给定的字符串是 _uniform_则输出"uniform"，否则输出"non-uniform"。

## 限制

* 1 ≤ T ≤ 256
* Length of s is 8

## 例子

```
Input
4
00000000
10101010
10000001
10010011

Output
uniform
non-uniform
uniform
non-uniform
```

## 解释

以上例子的转换数量分别是0，8，2和4。所以，第一个和第三个是uniform而第二个和第四个是non-uniform。

## 解法

本次我们使用C语言来实现。

第一个输入是一个整数T，我们需要接收这个输入，然后用一个while循环，这样就能循环处理多个测试情况了

```C
#include <stdio.h>

int main()
{
    int T;
    scanf("%d",&T);
    while(T)
    {
        //test case
    }
    return 0;
}
```

接着，我们开始处理每一个测试情况，每个测试情况的输入是一个长度为8的字符串，所以我们需要定义一个长度为9的char数组，其中前八位保存输入的字符串，最后一位保存字符串结束符'\0'。

```C
#include <stdio.h>

int main()
{
    int T;
    scanf("%d",&T);
    while(T)
    {
        char str[9];
        scanf("%s",str);
        //code
    }
    return 0;
}
```

然后我们需要遍历这个字符串，获得其中0-1或1-0转换的数量。判断前后两个字母是否一致，如果不一致就算是一个转换了，计算前后字母不一致的数量。要注意的是，这个字符串是循环的，所以还需要将最后一个字母同第一个字母进行比较。最后判断转换的数量确定字符串是否是uniform。

完整代码如下：

```C
#include <stdio.h>

int main()
{
    int T;
    scanf("%d",&T);
    while(T)
    {
        char str[9];
        scanf("%s",str);
        int transitionsCount = 0;
        for(int i = 0;i<8;++i)
        {
            if(str[i]!=str[(i+1)%8])
                ++transitionsCount;
        }
        if(transitionsCount<=2)
            printf("uniform");
        else
            printf("non-uniform");
    }
    return 0;
}
```

[Uniform Strings](http://www.learntop.tech/article/algorithm/1/)