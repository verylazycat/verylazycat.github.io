---
title: new&malloc&delete&free
tags: 杂项
---

[toc]

# malloc/free和new/delete的区别

- malloc/free是C/C++标准库的函数；new/delete是C++操作符。
- malloc/free只是动态分配内存空间/释放空间；new/delete除了分配空间还会调用构造函数和析构函数进行初始化与清理资源。
- malloc/free需要手动计算类型大小且返回值类型为void* ；new/delete可自动计算类型的大小，返回对应类型的指针。
- malloc/free管理内存失败会返回0；new/delete等的方式管理内存失败会抛出异常。

在C++ Primer书中有提到说： new/delete的表达式与标准库函数同名了，所以系统并没有重载new或delete表达式。new/delete真正的实现其实是依赖下面这几个内存管理接口的。

```c++
void *operator new(size_t);     //allocate an objectvoid *operator
delete(void *);    //free an objectvoid *operator
new[](size_t);     //allocate an arrayvoid *operator
delete[](void *);    //free an array
```

# malloc/free和new/delete的底层实现

## new的底层实现

```c++
// new.cpp
#include <cstdlib>
#include <new>
_C_LIB_DECLint __cdecl _callnewh(size_t size) _THROW1(_STD bad_alloc);
_END_C_LIB_DECLvoid *__CRTDECL operator new(size_t size) _THROW1(_STD bad_alloc)
{       // try to allocate size bytes      
       void *p;        
       while ((p = malloc(size)) == 0)
       if (_callnewh(size) == 0) 
       {       // report no memory                     
       _THROW_NCEE(_XSTD bad_alloc, );  
       }       
       return (p);  
}
```

## delete的底层实现

```c++
#include <cruntime.h>
#include <malloc.h>
#include <new.h>
#include <windows.h>
#include <rtcsup.h>
void operator delete( void * p )
{ 
    RTCCALLBACK(_RTC_Free_hook, (p, 0));
    free( p );
}
```

## new[]的底层实现

```c++
#include <new>
void *__CRTDECL operator new[](size_t count) _THROW1(std::bad_alloc)
{	// try to allocate count bytes for an array
    return (operator new(count));
}
```

## delete[]的底层实现

```c++
#ifdef CRTDLL
#undef CRTDLL
#endif
#ifdef MRTDLL
#undef MRTDLL
#endif
#define _USE_ANSI_CPP 
// suppress defaultlib directive for Std C++ Lib
#include <new>
extern void __CRTDECL operator delete[](void *ptr) _THROW0();
void __CRTDECL operator delete[](void *ptr,	const std::nothrow_t&) _THROW0()
{	// free an allocated object operator 
    delete[](ptr);
}
```

# malloc/free和new/delete的执行过程

## new的执行过程

```
new(int size) --> operator new() --> malloc() --> constructor function --> return ptr
```

## delete的执行过程

```
delete ptr --> destructor function --> operator delete() --> free
```

## new[]的执行过程

```
new[count] --> operator new[]() --> operator new() --> malloc() --> constructor function --> return ptr
```

## delete[]的执行过程

```
delete[] ptr --> destructor function --> operator delete[]() --> operator delete() --> free
```

- 依次调用指针指向对象数组中每个对象的析构函数
- 调用`operator delete[]()`，`operator delete[]()`再调用`operator delete`
- 底层用free执行`operator delete`表达式，依次释放内存
- `operator delete[]()`数组的个数存放在指针的前4位