---
title: C++正则表达式与算法
date: 2024-05-01 23:06:26
tags: 
- C++ 
- 正则表达式
- 算法
---
# 前言
我在编写一个可以快速生成CMake项目的控制台应用

他能够接受用户的输入来替换我写好的CMakeLists.txt中被&{}包围的字符串

那么我该如何实现这个功能,那么就是今天的主角,`"正则表达式"`

## 正则表达式
[菜鸟教程正则表达式](https://www.runoob.com/regexp/regexp-tutorial.html)

[在线正则表达式测试网站](https://regexr-cn.com/)

有了上面两个网站,我就不过多叙述正则表达式的基本语法了
>简要概括就是一个 `匹配器` 通过匹配符合的字符串 来对其进行操作
>例如判断密码是否符合条件,邮箱是否符合条件
>通过正则表达式判断下面的是否符合标准

```
1145141919810@qq.com 正确

1145141919810%qq.com 错误
```

为了解决我们的问题我们要了解在C++中如何使用正则表达式

>C++标准在C++11中引入了正则表达式。

我们需要包含的头文件

```c++
#include <regex>
```

在其中有几个较为重要的字段
`regex类` `regex_match函数` `regex_search函数` `regex_replace函数`

### regex类
```c++
std::regex pattern("正则表达式");
```
简单来说通过这个类你可以创建一个你需要的表达式
并在后给其他的函数
### regex_match函数
> 正则表达式匹配,匹配成功返回true或false

```c++
#include <regex>
#include <iostream>
int main(void)
{
    std::string text;
    std::cin >> text;
    //匹配首字母大写的字符串
    std::cout << std::regex_match(text, std::regex("([A-Z])\w+")) << std::endl;
    return 0;
}
```

### regex_search函数
> 正则表达式搜索匹配，即搜索字符串中所有符合规则的字符串。
```c++
#include <regex>
#include <iostream>
int main(void)
{
    std::string text;
    std::cin >> text;
    std::smatch matches;
    bool hasMatched = std::regex_search(text,matches,std::regex("(\\d{4})-(\\d{1,2})-(\\d{1,2})"));
    if(hasMatched)
    {
        std::cout << "Year:" << matches[1] << std::endl;
        std::cout << "Month:" << matches[2] << std::endl;
        std::cout << "Day:" << matches[3] << std::endl;
    }

    return 0;
}
```

### regex_replace函数
> 替换正则表达式匹配到的函数
```c++
#include <regex>
#include <iostream>
int main(void)
{
    regex_replace("I'm 114514M. I love 114514", std::regex("114514"), "You")
    return 0;
}
```

## 实战
对于一开始我们提出的问题,用正则表达式很好解决

给出解决问题的正则表达式
```c++
std::regex pattern(R"(\&\{\w+\})");
```