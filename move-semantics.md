# Move Semantics

## Move Semantics

> new bing的解释：
>
> ​[`copy assignment`](https://stackoverflow.com/questions/26772146/which-to-use-move-assignment-operator-vs-copy-assignment-operator)​[和](https://stackoverflow.com/questions/26772146/which-to-use-move-assignment-operator-vs-copy-assignment-operator)​[`move assignment`](https://stackoverflow.com/questions/26772146/which-to-use-move-assignment-operator-vs-copy-assignment-operator)​[都是C++中的赋值操作符，它们的区别在于它们如何处理右侧操作数。](https://stackoverflow.com/questions/26772146/which-to-use-move-assignment-operator-vs-copy-assignment-operator)​[`copy assignment`](https://stackoverflow.com/questions/26772146/which-to-use-move-assignment-operator-vs-copy-assignment-operator)​[使用右侧操作数的副本来赋值，而](https://stackoverflow.com/questions/26772146/which-to-use-move-assignment-operator-vs-copy-assignment-operator)​[`move assignment`](https://stackoverflow.com/questions/26772146/which-to-use-move-assignment-operator-vs-copy-assignment-operator)​[则使用右侧操作数的内容来赋值，同时将右侧操作数的内容设置为未定义状态。这意味着，如果右侧操作数是一个临时对象或一个可以安全移动的对象，那么使用](https://stackoverflow.com/questions/26772146/which-to-use-move-assignment-operator-vs-copy-assignment-operator)​[`move assignment`](https://stackoverflow.com/questions/26772146/which-to-use-move-assignment-operator-vs-copy-assignment-operator)​[可以避免不必要的内存分配和复制。如果右侧操作数是一个不可移动的对象或一个需要保留其状态的对象，则应使用](https://stackoverflow.com/questions/26772146/which-to-use-move-assignment-operator-vs-copy-assignment-operator)​[`copy assignment`](https://stackoverflow.com/questions/26772146/which-to-use-move-assignment-operator-vs-copy-assignment-operator)​[。](https://stackoverflow.com/questions/26772146/which-to-use-move-assignment-operator-vs-copy-assignment-operator)​[^1^](https://stackoverflow.com/questions/26772146/which-to-use-move-assignment-operator-vs-copy-assignment-operator)​[^2^](https://blog.csdn.net/zhangsj1007/article/details/80993224)​[^3^](https://zhuanlan.zhihu.com/p/85432613)

## 左值和右值

​![image](<.gitbook/assets/image 20231006111014 eszrdno.png>)​

* l-values live until the end of the scope
* r-values live until the end of the line

一些实例，感觉以判断这行结束后，值还live与否判断是不是r-value

​![image](<.gitbook/assets/image 20231006111451 xlpbdrz.png>)​

只有l-value才可以被通过&引用

​![image](<.gitbook/assets/image 20231006111920 3iy0wqx.png>)​

但是在vector拷贝赋值时

```cpp
template <typename T>
vector<T>& vector<T>::operator=(const vector<T>& other) {
if (&other == this) return *this;
_size = other._size;
_capacity = other._capacity;
delete[] _elems;
_elems = new T[other._capacity];
std::copy(other._elems, other._elems + other._size, _elems);
return *this;
}
```

额外的，为何要返回\*this？https://blog.csdn.net/annjeff/article/details/108952294

但是这样导致我们不可能通过如下的方式便捷的定义和赋值数组

```cpp
vector<int> vec;
vec = make_me_a_vec(123); 
```

若是我们通过如下方式重载make\_me\_a\_vec，这样会导致内存空间被无效利用，导致浪费：

```cpp
vector<int> make_me_a_vec(int num) {
     vector<int> res;
     while (num != 0) {
         res.push_back(num%10);
         num /= 10;
}
return res;
}
```

​![image](<.gitbook/assets/image 20231008174703 2zijsvy.png>)​

## move assignment

这样，并不能体现出使用引用传参的便利性，所以这时候就需要move assignment

那么编译器如何判断我们是否需要使用move assignment呢？

​![image](<.gitbook/assets/image 20231008174838 7n05897.png>)​

另外，我们需要重载运算符=，进行两个不同的赋值运算，这时候需要一个神秘的r-value reference `&&`​，具体在赋值问题的实现如下：

​![image](<.gitbook/assets/image 20231008175031 jkvnfc5.png>)​

我们可以继续优化右侧的move assignment，因为事实上有的地方也对变量在做拷贝

​![image](<.gitbook/assets/image 20231008175357 ruchkdk.png>)​

### std::move

std::move(x) doesn’t do anything except cast x as an r-value

​![image](<.gitbook/assets/image 20231008175512 vzjeey3.png>)​

这个函数还有许多用处...

但是还是要小心使用，比如在如下代码，会使vec1被free

​![image](<.gitbook/assets/image 20231008175726 ox4njx6.png>)​

\->Don’t use std::move outside of class definitions, never use it in application code!

## std::move and RAII

//todo 吃饭去了🍚
