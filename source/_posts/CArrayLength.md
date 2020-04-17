---
title: C/C++ 数组长度
comment: true
date: 2020-04-17 21:26:46
updated: 2020-04-17 21:26:46
categories: C++
tags:
---



C/C++ 获取数组长度的方法有如下几种：

1、算术表达式

```C++
#include <iostream>

int main() {
    int arr[4];
    std::cout << sizeof(arr) / sizeof(arr[0]) << std::endl; //输出4
}
```

2、函数模板参数自动推断

```C++
#include <iostream>

template<typename T, size_t N>
inline size_t Count(T (&arr)[N]) {
    return N;
}

int main() {
    int arr[4];
    std::cout << Count(arr) << std::endl; //输出4
}
```

3、标准C++模板库

```C++
#include <type_traits>  // 注意包含此头文件.
#include <iostream>
 
int main() {
    int arr[4];
    std::cout << std::extent<decltype(arr), 0>::value << std::endl; // 输出4
}
```

4、模板特化与自动类型推断

```C++
#include <iostream>
template<typename T>
class ComputeSize;

template<typename T, size_t N>
class ComputeSize<T[N]> {
public:
    static const size_t value = N;
};

int main() {
    int arr[4];
    std::cout << ComputeSize<decltype(arr)>::value << std::endl;
}
```

注意：

- 上述方法适用于静态数组，动态数组是无法获取数组元素个数的。

- 常用方法1来获取数组长度。

- 当数组作为参数传入函数时，传的是一个指针，指向数组的首地址，int 指针的大小一般为4Bytes，方法1的 sizeof(arr) / sizeof(arr[0]) 恒等于 1。解决方法有：约定全局，传入长度，封装数据结构（利用模板）。

利用模板的代码如下：

```C++
#include <iostream>
using namespace std;

template <class T>    //定义一个模板
int len(T& arr)  //利用模板引用数组a
{
	return sizeof(arr) / sizeof(arr[0]); //获取数组长度
	
}
 
int main()
{
	int a[10];
	postValude(a);
	cout << len(a) << endl; //将数组长度显示出来看结果是否正确
	
    return 0;
}

```

