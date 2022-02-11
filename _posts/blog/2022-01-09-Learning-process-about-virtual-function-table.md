---
layout: post
title: "关于虚函数表的学习过程"
date: 2022-01-09
author: codecat
toc: true
categories:
- blog
tags:
- 笔记
permalink: /blog/2022/Learning-process-about-virtual-function-table/
---

### 虚函数表

#### 简介

C++面向对象的多态性，这里我们就要涉及到虚函数了，接下来我们将用反汇编来学习虚函数的原理。

#### 直接调用与简介调用

这里的直接调用和间接调用时汇编层面上的，我们在看反汇编代码的时候经常会看到 call ---- (调用函数的那个call)，那这个呢就是直接调用，而还有一种形式是这样的call [----]那这个呢就是间接调用。

直接调用和简介调用反馈到十六进制编码上就是**E8(直接调用)**和**FF(简介调用)**

#### 虚函数

```c++
class Base{
public: 
	void Function1()
	{
		printf("Function1----");
	}
	virtual void Function2()
	{
		printf("Function2----");
	}
};
```

现在有这么一个类Base,它有两个成员函数一个不带有virtual的Function1和一个带有virtual的Function2，除此之外两函数之间基本上没啥区别，那现在我们来查看这两个函数调用的时候，它们的反汇编代码，看看它们的反汇编代码有什么区别没。



```c++
int main(int argc, char* argv[])
{

	Base base;
	base.Function1();
	base.Function2();
	return 0;

}
```



```assembly
00401030 55                   push        ebp
00401031 8B EC                mov         ebp,esp
00401033 83 EC 44             sub         esp,44h
00401036 53                   push        ebx
00401037 56                   push        esi
00401038 57                   push        edi
00401039 8D 7D BC             lea         edi,[ebp-44h]
0040103C B9 11 00 00 00       mov         ecx,11h
00401041 B8 CC CC CC CC       mov         eax,0CCCCCCCCh
00401046 F3 AB                rep stos    dword ptr [edi]
00401048 8D 4D FC             lea         ecx,[ebp-4]   //this指针
0040104B E8 BF FF FF FF       call        @ILT+10(Base::Base) (0040100f)
00401050 8D 4D FC             lea         ecx,[ebp-4]
00401053 E8 B2 FF FF FF       call        @ILT+5(Base::Function1) (0040100a)
00401058 8D 4D FC             lea         ecx,[ebp-4]
0040105B E8 A5 FF FF FF       call        @ILT+0(Base::Function2) (00401005)
00401060 33 C0                xor         eax,eax
00401062 5F                   pop         edi
00401063 5E                   pop         esi
00401064 5B                   pop         ebx
00401065 83 C4 44             add         esp,44h
00401068 3B EC                cmp         ebp,esp
0040106A E8 01 01 00 00       call        __chkesp (00401170)
0040106F 8B E5                mov         esp,ebp
00401071 5D                   pop         ebp
00401072 C3                   ret
```

我们可以看到调用这两个函数的时候使用的是直接调用还是间接调用`E8 B2 FF FF FF       call        @ILT+5(Base::Function1) (0040100a)`，`E8 A5 FF FF FF       call        @ILT+0(Base::Function2) (00401005)`

我们可以清楚的看到都是使用的直接调用，从反汇编的层面来看这样加不加virtual没有任何的区别。

那上面我们是使用对象直接调用的这两个函数，我们就可以总结了，如果我们**使用对象直接调用一个带virtual的函数的话它和调用普通函数是没啥区别的**。



那我们换一种方式再来进行试验

```c++
int main(int argc, char* argv[])
{
	Base base;
	Base* pbase=&base;
	pbase->Function1();
	pbase->Function2();
	

	return 0;

}
```

我创建了一个Base型的指针，然后使用指针来调用上面我们定义的这两个函数，查看其反汇编代码看看会有什么不同。

```assembly
00401030 55                   push        ebp
00401031 8B EC                mov         ebp,esp
00401033 83 EC 48             sub         esp,48h
00401036 53                   push        ebx
00401037 56                   push        esi
00401038 57                   push        edi
00401039 8D 7D B8             lea         edi,[ebp-48h]
0040103C B9 12 00 00 00       mov         ecx,12h
00401041 B8 CC CC CC CC       mov         eax,0CCCCCCCCh
00401046 F3 AB                rep stos    dword ptr [edi]
00401048 8D 4D FC             lea         ecx,[ebp-4]
0040104B E8 BF FF FF FF       call        @ILT+10(Base::Base) (0040100f)
00401050 8D 45 FC             lea         eax,[ebp-4]
00401053 89 45 F8             mov         dword ptr [ebp-8],eax
00401056 8B 4D F8             mov         ecx,dword ptr [ebp-8]
00401059 E8 AC FF FF FF       call        @ILT+5(Base::Function1) (0040100a)
0040105E 8B 4D F8             mov         ecx,dword ptr [ebp-8]
00401061 8B 11                mov         edx,dword ptr [ecx]
00401063 8B F4                mov         esi,esp
00401065 8B 4D F8             mov         ecx,dword ptr [ebp-8]
00401068 FF 12                call        dword ptr [edx]
0040106A 3B F4                cmp         esi,esp
0040106C E8 FF 00 00 00       call        __chkesp (00401170)//检查堆栈平衡的不用管
00401071 33 C0                xor         eax,eax
00401073 5F                   pop         edi
00401074 5E                   pop         esi
00401075 5B                   pop         ebx
00401076 83 C4 48             add         esp,48h
00401079 3B EC                cmp         ebp,esp
0040107B E8 F0 00 00 00       call        __chkesp (00401170)
00401080 8B E5                mov         esp,ebp
00401082 5D                   pop         ebp
00401083 C3                   ret
```

可以看到这次使用指针去调用和上面拿对象进行调用具有明显的不同，调用Function1函数的时候是这样的`E8 AC FF FF FF       call        @ILT+5(Base::Function1) (0040100a)`，而调用带有virtual关键字的Function函数的时候是这样的`FF 12                call        dword ptr [edx]`可以明显的看出来一个是直接调用一个是间接调用。

由此我们可以总结出，当使用指针来调用带有virtual的函数的时候，生成的是间接调用。

那除此之外虚函数还有什么特点呢？我们来继续做实验。



##### 虚函数的大小

```c++
class Base {
public:
	int x;
	int y;
	void Function1()
	{
		printf("Function1----\n");
	}
	virtual void Function2()
	{
		printf("Function2----\n");
	}
};
```

我把这个Base类改了一下，加上了两个int型的成员变量，那这个类多大呢？我们之前学过成员函数是不占有空间的，那这么看来这个类的大小应该是8字节咯。

我们来测试一下看看

```c++
int main(int argc, char* argv[])
{
	printf("%d\n",sizeof(Base));
	return 0;
}
```

<img src="C:\Users\86173\AppData\Roaming\Typora\typora-user-images\image-20220107203115476.png" alt="image-20220107203115476" style="zoom:80%;" />

可以看到结果为12字节，我们也说了之前学过普通的函数是不占有空间的，那么多出来的4字节只能是带有virtual的函数的大小了，这多出来的4字节真的是带有virtual函数的大小吗？

我们再来测一下，将上面的代码再改一改

```c++
class Base {
public:
	int x;
	int y;
	virtual void Function1()
	{
		printf("Function1----\n");
	}
	virtual void Function2()
	{
		printf("Function2----\n");
	}
};
```

可以看到我将两个成员函数都改为了带有virtual关键字的虚函数了，那我们再来测一测它的大小。

```c++
int main(int argc, char* argv[])
{
	printf("%d\n",sizeof(Base));
	return 0;
}
```

<img src="C:\Users\86173\AppData\Roaming\Typora\typora-user-images\image-20220107203554924.png" alt="image-20220107203554924" style="zoom:80%;" />

可以看到还是12，那说明多出来的那4个字节还不是虚函数的大小，那它是什么呢？



#### 虚函数表

从刚刚的实验我们知道接下来我们需要研究什么了，现在我再将上面的Base类改一改，重新测试一下，看看我们能不能得出问题的答案。

```c++
class Base{
public: 
	int a;
	int b;
	Base()
	{
		a=1;
		b=2;
	}
};
```

可以看到我将Base类的成员函数都给去掉了，加上了一个构造函数，我们来看看它的反汇编。

<img src="C:\Users\86173\AppData\Roaming\Typora\typora-user-images\image-20220107204506773.png" alt="image-20220107204506773" style="zoom:80%;" />

可以看到base里存储的数据是01 00 00 00和02 00 00 00也就是1和2，这是我们可以接受的。

那再将Base改一改

```c++
class Base{
public: 
	int a;
	int b;
	Base()
	{
		a=1;
		b=2;
	}
	virtual void Function1()
	{
		printf("Function1----");
	}
};
```

再来测一测对象base的数据

<img src="C:\Users\86173\AppData\Roaming\Typora\typora-user-images\image-20220108110526539.png" alt="image-20220108110526539" style="zoom:80%;" />

可以看到多出来的4字节的数据，而且这多出来的4字节还是在base对象的首地址。

多的这4字节存储的是一个地址，我们现在来看看这个地址里存储的是什么。

<img src="C:\Users\86173\AppData\Roaming\Typora\typora-user-images\image-20220108110553866.png" alt="image-20220108110553866" style="zoom:80%;" />

emmm，这里面存的值也看不出来是啥，那咱们就来测一测吧。

```c++
int main(int argc, char* argv[])
{
	Base base;
	Base* pbase=&base;
	pbase->Function1();
	return 0;
}
```

利用指针来调用这个虚函数，仔细看看它是怎么调用的。

```assembly
00401050 8D 45 F4             lea         eax,[ebp-0Ch]            //把ebp-0xc这块地址赋值给eax，ebp-0xc里是base
00401053 89 45 F0             mov         dword ptr [ebp-10h],eax  //给pbase指针赋值
00401056 8B 4D F0             mov         ecx,dword ptr [ebp-10h]  //将ebp-0x10里的值赋给ecx
00401059 8B 11                mov         edx,dword ptr [ecx]	   //将ecx里存放的值赋给edx
0040105B 8B F4                mov         esi,esp
0040105D 8B 4D F0             mov         ecx,dword ptr [ebp-10h]
00401060 FF 12                call        dword ptr [edx]           //调用edx里存放的值
00401062 3B F4                cmp         esi,esp
00401064 E8 07 01 00 00       call        __chkesp (00401170)
```

<img src="C:\Users\86173\AppData\Roaming\Typora\typora-user-images\image-20220108105908197.png" alt="image-20220108105908197" style="zoom:80%;" />

<img src="C:\Users\86173\AppData\Roaming\Typora\typora-user-images\image-20220108105959549.png" alt="image-20220108105959549" style="zoom:80%;" />

现在我们就知道了多出来的那4个字节是干啥的了，它是用来存放虚函数实际的地址的。



我们在多加一个虚函数来进行测试

```c++
class Base{
public: 
	int a;
	int b;
	Base()
	{
		a=1;
		b=2;
	}
	virtual void Function1()
	{
		printf("Function1----");
	}
	virtual void Function2()
	{
		printf("Function2----\n");
	}
};
```



```c++
int main(int argc, char* argv[])
{
	Base base;
	Base* pbase=&base;
	pbase->Function1();
	pbase->Function2();
	return 0;
}
```

观察反汇编看看有什么不一样吗？

```assembly
0040D716 8B 4D F0             mov         ecx,dword ptr [ebp-10h]
0040D719 8B 11                mov         edx,dword ptr [ecx]
0040D71B 8B F4                mov         esi,esp
0040D71D 8B 4D F0             mov         ecx,dword ptr [ebp-10h]
0040D720 FF 12                call        dword ptr [edx]
0040D722 3B F4                cmp         esi,esp
0040D724 E8 B7 39 FF FF       call        __chkesp (004010e0)
0040D729 8B 45 F0             mov         eax,dword ptr [ebp-10h]
0040D72C 8B 10                mov         edx,dword ptr [eax]
0040D72E 8B F4                mov         esi,esp
0040D730 8B 4D F0             mov         ecx,dword ptr [ebp-10h]
0040D733 FF 52 04             call        dword ptr [edx+4]  //只看这里就可以了
0040D736 3B F4                cmp         esi,esp
0040D738 E8 A3 39 FF FF       call        __chkesp (004010e0)
```



<img src="C:\Users\86173\AppData\Roaming\Typora\typora-user-images\image-20220108111200919.png" alt="image-20220108111200919" style="zoom:80%;" />

可以看到base对象里还是只多出了4字节，这4个字节是用来存放虚函数真实地址的。

<img src="C:\Users\86173\AppData\Roaming\Typora\typora-user-images\image-20220108111325918.png" alt="image-20220108111325918" style="zoom:80%;" />

可以看到0x00422fa4这个地址再加上4个字节的地方存放了一个新的地址，这个地址就是虚函数Function2的地址。

现在我们就搞清楚了多出来的4字节是干嘛的了，它是用来存储虚函数的实际地址的。



#### 课后作业

```c++
#include<stdio.h>
/// <summary>
/// 单继承无函数覆盖(打印Sub对象的虚函数表)
/// </summary>

class Base {
public:
	virtual void Function1()
	{
		printf("Function1----\n");
	}
	virtual void Function2()
	{
		printf("Function2----\n");
	}
	virtual void Function3()
	{
		printf("Function3----\n");
	}

};

class Sub:Base
{
public:
	virtual void Function4()
	{
		printf("Sub:Function_4....\n");
	}
	virtual void Function5()
	{
		printf("Sub:Function_5....\n");
	}
	virtual void Function6()
	{
		printf("Sub:Function_6....\n");
	}

};

int main(int argc, char* argv[])
{
	Sub s;
	typedef void(*pFunction)(void);
	pFunction pFun=nullptr;
	for (size_t i = 0; i < 6; i++)
	{
		printf("地址:%x\n",*(((int*)(*((int*)&s)))+i));
		pFun = (pFunction)(*(((int*)(*((int*)&s))) + i));
		pFun();
	}
	return 0;
}
```

```c++
#include<stdio.h>
/// <summary>
/// 单继承有覆盖(打印Sub对象的虚函数表)
/// </summary>

class Base {
public:
	virtual void Function1()
	{
		printf("Base:Function1!-----");
	}
	virtual void Function2()
	{
		printf("Base:Function2!-----");
	}
	virtual void Function3()
	{
		printf("Base:Function3!-----");
	}
};

class Sub :Base {
public:
	virtual void Function1()
	{
		printf("Sub:Function1!-----");
	}
	virtual void Function2()
	{
		printf("Sub:Function2!-----");
	}
	virtual void Function6()
	{
		printf("Sub:Function6!-----");
	}
};

int main()
{
	Sub s;
	typedef void (*pFunction)(void);
	pFunction pfun = nullptr;
	for (size_t i = 0; i < 4; i++)
	{
		printf("地址:%x\n",*(((int*)(*(int*)&s))+i));
		pfun = (pFunction)(*(((int*)(*(int*)&s)) + i));
		pfun();
		printf("\n");
	}
	return 0;
}
```