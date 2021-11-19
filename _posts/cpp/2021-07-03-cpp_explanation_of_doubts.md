---
layout:     post
title:      c++释疑
subtitle:   关于c++的特性
date:       2021-07-03
author:     wenjh
header-img: img/post-bg-balloons.jpg
catalog:    false
tags:
    - c++
---

![]({{site.baseurl}}/img/logo.png)

# 1. c++重载

## 1.1 运算符重载

> **基类中将运算符重载为虚函数是可行的**



例如：

```cpp
#include<iostream>
struct A
{
	virtual void operator()() = 0;
};
struct B : public A
{
	virtual void operator()()
	{
		std::cout << "This is class B." << std::endl;
	}
};
int main()
{
	A* b = new B;
	(*b)();
	int i;
	std::cin >> i;
	return i;
}
```



输出为：

```sh
This is class B.
```

