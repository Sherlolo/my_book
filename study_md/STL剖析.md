# STL剖析

> gnu 2.9

[TOC]

# STL思想

- 容器(Container)
- 分配器(Allocators)
- 算法(Algorithms)
- 迭代器(Iterators)
- 适配器(Adapters)
- 防函数(Functors)

![](./img/hj_13.jpg)

- 迭代器或函数传递时的区间采用：“前闭后开”区间 [)

# 空间配置器(allocator)

空间配置器的思想：

![](./img/hj_14.png)

![](./img/hj_15.png)



## 空间配置的标准接口

根据STL标准，以下是allocator的必要接口

```c++
allocator::value_type;
allocator::pointer;
allocator::const_pointer;
allocator::reference;
allocator::const_reference;
allocator::size_type;
allocator::difference_type;

allocator::rebind();	//一个嵌套的class template,class rebind<U>拥有唯一成员：typedef allocator<U> other;
allocator::allocator();	//default constructor
allocator::allocator(const allocator&);	//copy constructor
allocator::~allocator();	//destructor
const_pointer allocator::address(reference x) const; //返回x的const对象地址

pointer allocator::allocate(size_type n, const void* = 0);	//配置空间 第二个参数是提示
void allocator::deallocate(pointer p, size_type n);	//归还之前的空间
size_type allocator::max_size() const;	//返回可配置空间的最大量
void allocator::construct(pointer p, const T& x); //new((void*)p) T(x);
void allocator::destroy(pointer p); //p->~T()
```

## SGI空间配置器

SGI空间配置器有两级，如下：

- 第一级配置器: std::allocator 标准malloc和free
- 第二级配置器: std::alloc 内存池

### 第一级配置器: std::allocator

设计思想：

- 第一级配置器采用malloc()、realloc()、free()等c函数来执行
- 并重新实现了new的c++ new_handler的机制（抛出异常前 会调用指定的处理例程）
- 以realloc()提供reallocate()接口

```c++
// 一个简单的分配器
// defalloc.h
template <class T>
inline T* allocate(ptrdiff_t size, T*) {
    set_new_handler(0);
    T* tmp = (T*)(::operator new((size_t)(size * sizeof(T))));
    if (tmp == 0) {
	cerr << "out of memory" << endl; 
	exit(1);
    }
    return tmp;
}


template <class T>
inline void deallocate(T* buffer) {
    ::operator delete(buffer);
}

template <class T>
class allocator {
public:
    typedef T value_type;
    typedef T* pointer;
    typedef const T* const_pointer;
    typedef T& reference;
    typedef const T& const_reference;
    typedef size_t size_type;
    typedef ptrdiff_t difference_type;
    
    pointer allocate(size_type n) { 
		return ::allocate((difference_type)n, (pointer)0);
    }
    
    void deallocate(pointer p) { ::deallocate(p); }
    
    pointer address(reference x) { return (pointer)&x; }
    
    const_pointer const_address(const_reference x) { 
		return (const_pointer)&x; 
    }
    
    size_type init_page_size() { 
		return max(size_type(1), size_type(4096/sizeof(T))); 
    }
    size_type max_size() const { 
		return max(size_type(1), size_type(UINT_MAX/sizeof(T))); 
    }
};

//特化版本
class allocator<void> {
public:
    typedef void* pointer;
};

//第一级分配器
template <int inst>
class __malloc_alloc_template 
{
private:

    static void *oom_malloc(size_t);

    static void *oom_realloc(void *, size_t);

    #ifndef __STL_STATIC_TEMPLATE_MEMBER_BUG
        static void (* __malloc_alloc_oom_handler)();
    #endif

public:

    static void * allocate(size_t n)
    {
        void *result = malloc(n);
        if (0 == result) result = oom_malloc(n);
        return result;
    }

    static void deallocate(void *p, size_t /* n */)
    {
        free(p);
    }

    static void * reallocate(void *p, size_t /* old_sz */, size_t new_sz)
    {
        void * result = realloc(p, new_sz);
        if (0 == result) result = oom_realloc(p, new_sz);
        return result;
    }

    static void (* set_malloc_handler(void (*f)()))()
    {
        void (* old)() = __malloc_alloc_oom_handler;
        __malloc_alloc_oom_handler = f;
        return(old);
    }

}; //class _malloc_alloc_template

// malloc_alloc out-of-memory handling

#ifndef __STL_STATIC_TEMPLATE_MEMBER_BUG
template <int inst>
void (* __malloc_alloc_template<inst>::__malloc_alloc_oom_handler)() = 0;
#endif

//oom_malloc
template <int inst>
void * __malloc_alloc_template<inst>::oom_malloc(size_t n)
{
    void (* my_malloc_handler)();
    void *result;

    for (;;) {
        my_malloc_handler = __malloc_alloc_oom_handler;
        if (0 == my_malloc_handler) { __THROW_BAD_ALLOC; }
        (*my_malloc_handler)();
        result = malloc(n);
        if (result) return(result);
    }
}

template <int inst>
void * __malloc_alloc_template<inst>::oom_realloc(void *p, size_t n)
{
    void (* my_malloc_handler)();
    void *result;

    for (;;) {
        my_malloc_handler = __malloc_alloc_oom_handler;
        if (0 == my_malloc_handler) { __THROW_BAD_ALLOC; }
        (*my_malloc_handler)();
        result = realloc(p, n);
        if (result) return(result);
    }
}

typedef __malloc_alloc_template<0> malloc_alloc;

template<class T, class Alloc>
class simple_alloc {

public:
    static T *allocate(size_t n)
                { return 0 == n? 0 : (T*) Alloc::allocate(n * sizeof (T)); }
    static T *allocate(void)
                { return (T*) Alloc::allocate(sizeof (T)); }
    static void deallocate(T *p, size_t n)
                { if (0 != n) Alloc::deallocate(p, n * sizeof (T)); }
    static void deallocate(T *p)
                { Alloc::deallocate(p, sizeof (T)); }
};
```

### 第二级配置器: std::alloc

SGI设计思想：

- 向system heap要求空间
- 考虑多线程状态
- 考虑内存不足的应变措施
- 考虑过多“小型区块”可能造成的内存碎片问题

```c++
// 8 16 24 ..... 128
enum {__ALIGN = 8}; //小型区块的上调边界
enum {__MAX_BYTES = 128}; //小型区块的上线
enum {__NFREELISTS = __MAX_BYTES/__ALIGN}; //free-lists的个数

template <bool threads, int inst>
class __default_alloc_template {
private:
    
    //上调至8的倍数
    static size_t ROUND_UP(size_t bytes) {
        return (((bytes) + __ALIGN-1) & ~(__ALIGN - 1));
    }
	//节点构造 
    union obj {
        union obj * free_list_link;
        char client_data[1];    /* The client sees this.        */
    };
    
private:
    
    //free-lists 内含16个节点
    static obj * __VOLATILE free_list[__NFREELISTS]; 
    
    // 根据区块大小 选择第n号free-lists
    static  size_t FREELIST_INDEX(size_t bytes) {
        return (((bytes) + __ALIGN-1)/__ALIGN - 1);
    }

  	// 分配一块大小为n的区块空间并返回
    static void *refill(size_t n);
    // 配置nobjs个大小为size的区块
    // nobjs可能会过程中该表
    static char *chunk_alloc(size_t size, int &nobjs);
private:
    // 内存池的数据
    static char *start_free;
    static char *end_free;
    static size_t heap_size;

public:

  static void * allocate(size_t n)
  {
    obj * __VOLATILE * my_free_list;
    obj * __RESTRICT result;
	
    //大于128就呼叫第一级配置器
    if (n > (size_t) __MAX_BYTES) {
        return(malloc_alloc::allocate(n));
    }
      
    my_free_list = free_list + FREELIST_INDEX(n);
    result = *my_free_list;
    if (result == 0) {
        //没有可用的free list 从新填充free list
        void *r = refill(ROUND_UP(n));
        return r;
    }
    *my_free_list = result -> free_list_link;
    return (result);
  };

  /* p may not be 0 */
  static void deallocate(void *p, size_t n)
  {
    obj *q = (obj *)p;
    obj * __VOLATILE * my_free_list;
	
    //大于128就呼叫第一级配置器
    if (n > (size_t) __MAX_BYTES) {
        malloc_alloc::deallocate(p, n);
        return;
    }
    my_free_list = free_list + FREELIST_INDEX(n);
    q -> free_list_link = *my_free_list;
    *my_free_list = q;
  }

  static void * reallocate(void *p, size_t old_sz, size_t new_sz);

}; // class __default_alloc_template


template <bool threads, int inst>
char *__default_alloc_template<threads, inst>::start_free = 0;

template <bool threads, int inst>
char *__default_alloc_template<threads, inst>::end_free = 0;

template <bool threads, int inst>
size_t __default_alloc_template<threads, inst>::heap_size = 0;

template <bool threads, int inst>
__default_alloc_template<threads, inst>::obj * __VOLATILE
__default_alloc_template<threads, inst> ::free_list[__NFREELISTS] 
    = {0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, };



// chunk_alloc
// 关键代码从内存池分配空间
template <bool threads, int inst>
char*
__default_alloc_template<threads, inst>::chunk_alloc(size_t size, int& nobjs)
{
    char * result;
    size_t total_bytes = size * nobjs; // nobjs初始为20
    size_t bytes_left = end_free - start_free; // 内存池中剩余空间

    if (bytes_left >= total_bytes) { //内存池空间完全满足需求量
        result = start_free;
        start_free += total_bytes;
        return(result);
    } else if (bytes_left >= size) { // 内存池空间不能完全满足 但能提供一个以上的区块
        nobjs = bytes_left/size;
        total_bytes = size * nobjs;
        result = start_free;
        start_free += total_bytes;
        return(result);
    } else { // 内存池空间连一块空间都无法满足
        size_t bytes_to_get = 2 * total_bytes + ROUND_UP(heap_size >> 4);
        // 从内存池中残余空间中进行分配
        if (bytes_left > 0) {
            obj * __VOLATILE * my_free_list =
                        free_list + FREELIST_INDEX(bytes_left);

            ((obj *)start_free) -> free_list_link = *my_free_list;
            *my_free_list = (obj *)start_free;
        }
        
        // 从堆空间中分配新区块
        start_free = (char *)malloc(bytes_to_get);
        if (0 == start_free) {
            // 堆空间不足 利用剩余的东西搜索新空间
            int i;
            obj * __VOLATILE * my_free_list, *p;
            for (i = size; i <= __MAX_BYTES; i += __ALIGN) {
                my_free_list = free_list + FREELIST_INDEX(i);
                p = *my_free_list;
                if (0 != p) {
                    *my_free_list = p -> free_list_link;
                    start_free = (char *)p;
                    end_free = start_free + i;
                }
      		}
            end_free = 0;	// 山穷水尽 使用out of handler机制
            start_free = (char *)malloc_alloc::allocate(bytes_to_get);
        }
        heap_size += bytes_to_get;
        end_free = start_free + bytes_to_get;
        return(chunk_alloc(size, nobjs)); // 递归调用本身 为了修正nobjs
    }
}

// refill
// free list中没有可用空间时，会调用refill从新填充空间
template <bool threads, int inst>
void* __default_alloc_template<threads, inst>::refill(size_t n)
{
    int nobjs = 20;
    
    // 尝试使用chunk_alloc分配新空间 nobjs时pass by reference
    char * chunk = chunk_alloc(n, nobjs);
    obj * __VOLATILE * my_free_list;
    obj * result;
    obj * current_obj, * next_obj;
    int i;
	
    // 如果只有一个区块 直接返回使用
    if (1 == nobjs) return(chunk);
    
    // 否则从新调整区块空间
    my_free_list = free_list + FREELIST_INDEX(n);

   
    result = (obj *)chunk;
    *my_free_list = next_obj = (obj *)(chunk + n);
    for (i = 1; ; i++) {
        current_obj = next_obj;
        next_obj = (obj *)((char *)next_obj + n);
        if (nobjs - 1 == i) {
            current_obj -> free_list_link = 0;
            break;
        } else {
            current_obj -> free_list_link = next_obj;
        }
    }
    return(result);
}


template <bool threads, int inst>
void*
__default_alloc_template<threads, inst>::reallocate(void *p,
                                                    size_t old_sz,
                                                    size_t new_sz)
{
    void * result;
    size_t copy_sz;

    if (old_sz > (size_t) __MAX_BYTES && new_sz > (size_t) __MAX_BYTES) {
        return(realloc(p, new_sz));
    }
    if (ROUND_UP(old_sz) == ROUND_UP(new_sz)) return(p);
    result = allocate(new_sz);
    copy_sz = new_sz > old_sz? old_sz : new_sz;
    memcpy(result, p, copy_sz);
    deallocate(p, old_sz);
    return(result);
}
```

## 构造器(construct & destroy)

设计思想：

- 在对对象进行析构时，如果析构函数时默认的，将什么也不做。

![](./img/hj_16.png)

```c++
template <class T>
inline void destroy(T* pointer) {
    pointer->~T();
}

template <class T1, class T2>
inline void construct(T1* p, const T2& value) {
  new (p) T1(value);
}

template <class ForwardIterator>
inline void
__destroy_aux(ForwardIterator first, ForwardIterator last, __false_type) {
  for ( ; first < last; ++first)
    destroy(&*first);
}

template <class ForwardIterator> 
inline void __destroy_aux(ForwardIterator, ForwardIterator, __true_type) {}

template <class ForwardIterator, class T>
inline void __destroy(ForwardIterator first, ForwardIterator last, T*) {
  typedef typename __type_traits<T>::has_trivial_destructor trivial_destructor;
  __destroy_aux(first, last, trivial_destructor());
}

template <class ForwardIterator>
inline void destroy(ForwardIterator first, ForwardIterator last) {
  __destroy(first, last, value_type(first));
}

inline void destroy(char*, char*) {}
inline void destroy(wchar_t*, wchar_t*) {}
```

## 内存基本处理工具(uninitialized_)

STL定于有5个全局函数，作用于未初始化空间上，这些功能对容器的实现非常有帮助：

- uninitialized_copy(InputIterator first, InputIterator last, ForwardIterator result)	将[first,last)指向的范围拷贝到result
- uninitialized_fill(ForwardIterator first, ForwardIterator last, const T& x)  将x值复制到[first,last)指向的范围
- uninitialized_fill_n(ForwardIterator first, Size n, const T& x)  将x值复制到[first, first + n)指向的范围
- POD指Plain Old Data, 也就是标量类型或传统类型，对POD类型将采取高阶的算法实现

![](./img/hj_17.png)



> 为什么要区分未初始化空间？
>
> 在未初始空间上直接赋值或构造都是可行的
>
> 但是考虑内含指针的对象：在已构造空间上进行赋值，会造成内存泄漏。



```c++
// uninitialized_copy
template <class InputIterator, class ForwardIterator>
inline ForwardIterator 
__uninitialized_copy_aux(InputIterator first, InputIterator last,
                         ForwardIterator result,
                         __true_type) {
  return copy(first, last, result);
}

template <class InputIterator, class ForwardIterator>
ForwardIterator 
__uninitialized_copy_aux(InputIterator first, InputIterator last,
                         ForwardIterator result,
                         __false_type) {
  ForwardIterator cur = result;
  __STL_TRY {
    for ( ; first != last; ++first, ++cur)
      construct(&*cur, *first);
    return cur;
  }
  __STL_UNWIND(destroy(result, cur));
}


template <class InputIterator, class ForwardIterator, class T>
inline ForwardIterator
__uninitialized_copy(InputIterator first, InputIterator last,
                     ForwardIterator result, T*) {
  typedef typename __type_traits<T>::is_POD_type is_POD;
  return __uninitialized_copy_aux(first, last, result, is_POD());
}

template <class InputIterator, class ForwardIterator>
inline ForwardIterator
  uninitialized_copy(InputIterator first, InputIterator last,
                     ForwardIterator result) {
  return __uninitialized_copy(first, last, result, value_type(result));
}

// char* wchat* partilized
inline char* uninitialized_copy(const char* first, const char* last,
                                char* result) {
  memmove(result, first, last - first);
  return result + (last - first);
  
}

inline wchar_t* uninitialized_copy(const wchar_t* first, const wchar_t* last,
                                   wchar_t* result) {
  memmove(result, first, sizeof(wchar_t) * (last - first));
  return result + (last - first);
}

// uninitalized_copy_n
template <class InputIterator, class Size, class ForwardIterator>
pair<InputIterator, ForwardIterator>
__uninitialized_copy_n(InputIterator first, Size count,
                       ForwardIterator result,
                       input_iterator_tag) {
  ForwardIterator cur = result;
  __STL_TRY {
    for ( ; count > 0 ; --count, ++first, ++cur) 
      construct(&*cur, *first);
    return pair<InputIterator, ForwardIterator>(first, cur);
  }
  __STL_UNWIND(destroy(result, cur));
}

template <class RandomAccessIterator, class Size, class ForwardIterator>
inline pair<RandomAccessIterator, ForwardIterator>
__uninitialized_copy_n(RandomAccessIterator first, Size count,
                       ForwardIterator result,
                       random_access_iterator_tag) {
  RandomAccessIterator last = first + count;
  return make_pair(last, uninitialized_copy(first, last, result));
}

template <class InputIterator, class Size, class ForwardIterator>
inline pair<InputIterator, ForwardIterator>
uninitialized_copy_n(InputIterator first, Size count,
                     ForwardIterator result) {
  return __uninitialized_copy_n(first, count, result,
                                iterator_category(first));
}

// Valid if copy construction is equivalent to assignment, and if the
//  destructor is trivial.
template <class ForwardIterator, class T>
inline void
__uninitialized_fill_aux(ForwardIterator first, ForwardIterator last, 
                         const T& x, __true_type)
{
  fill(first, last, x);
}

template <class ForwardIterator, class T>
void
__uninitialized_fill_aux(ForwardIterator first, ForwardIterator last, 
                         const T& x, __false_type)
{
  ForwardIterator cur = first;
  __STL_TRY {
    for ( ; cur != last; ++cur)
      construct(&*cur, x);
  }
  __STL_UNWIND(destroy(first, cur));
}

template <class ForwardIterator, class T, class T1>
inline void __uninitialized_fill(ForwardIterator first, ForwardIterator last, 
                                 const T& x, T1*) {
  typedef typename __type_traits<T1>::is_POD_type is_POD;
  __uninitialized_fill_aux(first, last, x, is_POD());
                   
}

template <class ForwardIterator, class T>
inline void uninitialized_fill(ForwardIterator first, ForwardIterator last, 
                               const T& x) {
  __uninitialized_fill(first, last, x, value_type(first));
}

// Valid if copy construction is equivalent to assignment, and if the
//  destructor is trivial.
template <class ForwardIterator, class Size, class T>
inline ForwardIterator
__uninitialized_fill_n_aux(ForwardIterator first, Size n,
                           const T& x, __true_type) {
  return fill_n(first, n, x);
}

template <class ForwardIterator, class Size, class T>
ForwardIterator
__uninitialized_fill_n_aux(ForwardIterator first, Size n,
                           const T& x, __false_type) {
  ForwardIterator cur = first;
  __STL_TRY {
    for ( ; n > 0; --n, ++cur)
      construct(&*cur, x);
    return cur;
  }
  __STL_UNWIND(destroy(first, cur));
}

template <class ForwardIterator, class Size, class T, class T1>
inline ForwardIterator __uninitialized_fill_n(ForwardIterator first, Size n,
                                              const T& x, T1*) {
  typedef typename __type_traits<T1>::is_POD_type is_POD;
  return __uninitialized_fill_n_aux(first, n, x, is_POD());
                                    
}

template <class ForwardIterator, class Size, class T>
inline ForwardIterator uninitialized_fill_n(ForwardIterator first, Size n,
                                            const T& x) {
  return __uninitialized_fill_n(first, n, x, value_type(first));
}

// Copies [first1, last1) into [result, result + (last1 - first1)), and
//  copies [first2, last2) into
//  [result, result + (last1 - first1) + (last2 - first2)).

template <class InputIterator1, class InputIterator2, class ForwardIterator>
inline ForwardIterator
__uninitialized_copy_copy(InputIterator1 first1, InputIterator1 last1,
                          InputIterator2 first2, InputIterator2 last2,
                          ForwardIterator result) {
  ForwardIterator mid = uninitialized_copy(first1, last1, result);
  __STL_TRY {
    return uninitialized_copy(first2, last2, mid);
  }
  __STL_UNWIND(destroy(result, mid));
}

// Fills [result, mid) with x, and copies [first, last) into
//  [mid, mid + (last - first)).
template <class ForwardIterator, class T, class InputIterator>
inline ForwardIterator 
__uninitialized_fill_copy(ForwardIterator result, ForwardIterator mid,
                          const T& x,
                          InputIterator first, InputIterator last) {
  uninitialized_fill(result, mid, x);
  __STL_TRY {
    return uninitialized_copy(first, last, mid);
  }
  __STL_UNWIND(destroy(result, mid));
}

// Copies [first1, last1) into [first2, first2 + (last1 - first1)), and
//  fills [first2 + (last1 - first1), last2) with x.
template <class InputIterator, class ForwardIterator, class T>
inline void
__uninitialized_copy_fill(InputIterator first1, InputIterator last1,
                          ForwardIterator first2, ForwardIterator last2,
                          const T& x) {
  ForwardIterator mid2 = uninitialized_copy(first1, last1, first2);
  __STL_TRY {
    uninitialized_fill(mid2, last2, x);
  }
  __STL_UNWIND(destroy(first2, mid2));
}
```



# 迭代器(Iterator)和traits编程技术

STL中心思想将数据容器(container)和算法(algorithm)分开，彼此独立设计。中间通过迭代器连接(Iterator)

## traits编程技术

### traits设计思想

为了更好的封装性，会将迭代器的开发将给容器的设计者，算法再通过traits提取型别。

traits扮演特性萃取机的功能：

```c++
template <class I>
struct iterator_traits
{
	typedef typename I::value_type value_type;
}

// 针对指针类型的偏特化
template <class I>
struct iterator_traits<I*>
{
	typedef T value_type;
}

// 使用
template <class I>
typename iterator_traits<I>::value_type
fun(I iter)
{
    return *iter;
}
```

## type_traits

iterator_traits负责萃取迭代器的特性，type_traits负责萃取型别的特性。

type_traits萃取的型别包括：

```c++
__STL_TEMPLATE_NULL struct __type_traits<char> {
   typedef __true_type    has_trivial_default_constructor;
   typedef __true_type    has_trivial_copy_constructor;
   typedef __true_type    has_trivial_assignment_operator;
   typedef __true_type    has_trivial_destructor;
   typedef __true_type    is_POD_type;
};
```

针对type_traits方法，可以在设计算法时将传统型别和自定义的类别区别开来

## 迭代器

### 迭代器型别和类型

迭代器中会定义5种型别，通过traits萃取出来:

```c++
template <class I>
struct iterator_traits
{
    typedef typename I::iterator_category 	iterator_category;	
    typedef typename I::value_type			value_type;			//迭代器所值对象的型别
    typedef typename I::difference_type		difference_type;	//两个迭代器的距离 int8
    typedef typename I::pointer				pointer;			//迭代器所值对象的指针
    typedef typename I::reference			reference;			//迭代器所值对象的引用 分为const和非const
}
```

迭代器的类型：

- Input Iterator: 不允许外界改变，只读。
- Output Iterator: 只写
- Forward Iterator: 可读写
- Bidirectional Iterator: 可双向移动
- Random Access Iterator: 除前四种功能外，还可以：p+n, p1 - p2, p1 < p2

![](./img/hj_18.png)

### 迭代器设计源码

```c++
struct input_iterator_tag {};
struct output_iterator_tag {};
struct forward_iterator_tag : public input_iterator_tag {};
struct bidirectional_iterator_tag : public forward_iterator_tag {};
struct random_access_iterator_tag : public bidirectional_iterator_tag {};

//自行开发的迭代器最好继承如下类
template <class Category, class T, class Distance = ptrdiff_t,
          class Pointer = T*, class Reference = T&>
struct iterator {
  typedef Category  iterator_category;
  typedef T         value_type;
  typedef Distance  difference_type;
  typedef Pointer   pointer;
  typedef Reference reference;
};

template <class Iterator>
struct iterator_traits {
  typedef typename Iterator::iterator_category iterator_category;
  typedef typename Iterator::value_type        value_type;
  typedef typename Iterator::difference_type   difference_type;
  typedef typename Iterator::pointer           pointer;
  typedef typename Iterator::reference         reference;
};

template <class T>
struct iterator_traits<T*> {
  typedef random_access_iterator_tag iterator_category;
  typedef T                          value_type;
  typedef ptrdiff_t                  difference_type;
  typedef T*                         pointer;
  typedef T&                         reference;
};

template <class T>
struct iterator_traits<const T*> {
  typedef random_access_iterator_tag iterator_category;
  typedef T                          value_type;
  typedef ptrdiff_t                  difference_type;
  typedef const T*                   pointer;
  typedef const T&                   reference;
};

//如下将产生一个临时对象来方便判断
template <class Iterator>
inline typename iterator_traits<Iterator>::iterator_category
iterator_category(const Iterator&) {
  typedef typename iterator_traits<Iterator>::iterator_category category;
  return category();
}

template <class Iterator>
inline typename iterator_traits<Iterator>::difference_type*
distance_type(const Iterator&) {
  return static_cast<typename iterator_traits<Iterator>::difference_type*>(0);
}

template <class Iterator>
inline typename iterator_traits<Iterator>::value_type*
value_type(const Iterator&) {
  return static_cast<typename iterator_traits<Iterator>::value_type*>(0);
}
```



# 序列式容器

## vector

### vector概述

vector的数据安排以及操作方式和array非常相似。它们都是连续空间，两者唯一差别：array是静态空间，vector是动态空间。

![](./img/hj_19.png)

> G4.9

![](./img/hj_20.png)



### vector定义

```c++
template <class T, class Alloc = alloc>
class vector {
public:
    typedef T value_type;
    typedef value_type* pointer;
    typedef const value_type* const_pointer;
    typedef value_type* iterator; //vector的迭代器是指针 也是random access iterator类型
    typedef const value_type* const_iterator;
    typedef value_type& reference;
    typedef const value_type& const_reference;
    typedef size_t size_type;
    typedef ptrdiff_t difference_type;
    
protected:
    typedef simple_alloc<value_type, Alloc> data_allocator;
    iterator start;
    iterator finish;
    iterator end_of_storage;
	
    void insert_aux(iterator position, const T& x);
    void deallocate() {
        if (start) data_allocator::deallocate(start, end_of_storage - start);
    }

    void fill_initialize(size_type n, const T& value) {
        start = allocate_and_fill(n, value);
        finish = start + n;
        end_of_storage = finish;
    }

public:
    iterator begin() { return start; }
    const_iterator begin() const { return start; }
    iterator end() { return finish; }
    const_iterator end() const { return finish; }
    reverse_iterator rbegin() { return reverse_iterator(end()); }
    const_reverse_iterator rbegin() const { 
        return const_reverse_iterator(end()); 
    }
    reverse_iterator rend() { return reverse_iterator(begin()); }
    const_reverse_iterator rend() const { 
        return const_reverse_iterator(begin()); 
    }
    size_type size() const { return size_type(end() - begin()); }
    size_type max_size() const { return size_type(-1) / sizeof(T); }
    size_type capacity() const { return size_type(end_of_storage - begin()); }
    bool empty() const { return begin() == end(); }
    reference operator[](size_type n) { return *(begin() + n); }
    const_reference operator[](size_type n) const { return *(begin() + n); }
    
    // ctor
    vector() : start(0), finish(0), end_of_storage(0) {}
    vector(size_type n, const T& value) { fill_initialize(n, value); }
    vector(int n, const T& value) { fill_initialize(n, value); }
    vector(long n, const T& value) { fill_initialize(n, value); }
    explicit vector(size_type n) { fill_initialize(n, T()); }
    
    // dctor
    ~vector() { 
        destroy(start, finish);
        deallocate();
    }
    
    // front back
    reference front() { return *begin(); }
    const_reference front() const { return *begin(); }
    reference back() { return *(end() - 1); }
    const_reference back() const { return *(end() - 1); }
    
    
    void push_back(const T& x) {
        if (finish != end_of_storage) {
            construct(finish, x);
            ++finish;
        }
        else
            insert_aux(end(), x);
    }
    
    void pop_back() {
        --finish;
        destroy(finish);
    }
    
    iterator erase(iterator position) {
        if (position + 1 != end())
            copy(position + 1, finish, position);
        --finish;
        destroy(finish);
        return position;
    }
    
    void resize(size_type new_size, const T& x) {
        if (new_size < size()) 
            erase(begin() + new_size, end());
        else
            insert(end(), new_size - size(), x);
    }
    void resize(size_type new_size) { resize(new_size, T()); }
    void clear() { erase(begin(), end()); }
    
}; // class vector
```



## list

### list概述

- list是一个环状双向链表
- list其物理结构：链表
- 对元素的插入和删除为常数时间
- list不能随机访问
- list和vector是两个最常用的容器

### list的定义

> list的节点

```c++
template <class T>
struct __list_node
{
	typedef void* void_pointer;
    void_pointer prev;	//指向前一元素指针
    void_pointer next;	//指向后一元素指针
    T data；	//数据节点
};
```

> list的迭代器
>
> - list_iterator是bidirectional_iterator_tag 即可前后，但不可随机访问
>
> - list.end()是尾后指针，本身指向一个空节点

![](./img/hj_21.png)

```c++
template <class T, class Ref, class Ptr>
struct __list_iterator
{
	typedef __list_iterator<T, T&, T*>	iterator;
    typedef __list_iterator<T, Ref, Ptr> self;
    
    typedef bidirectional_iterator_tag iterator_category;
    typedef T value_type;
    typedef Ptr pointer;
    typedef Ref reference;
    typedef __list_node<T>* link_type;
    typedef size_t size_type;
    typedef ptrdiff_t difference_type;

    link_type node;

    __list_iterator(link_type x) : node(x) {}
    __list_iterator() {}
    __list_iterator(const iterator& x) : node(x.node) {}

    bool operator==(const self& x) const { return node == x.node; }
    bool operator!=(const self& x) const { return node != x.node; }
    reference operator*() const { return (*node).data; }
    
    pointer operator->() const { return &(operator*()); }
    self& operator++() { 
        node = (link_type)((*node).next);
        return *this;
    }
    self operator++(int) { 
        self tmp = *this;
        ++*this;
        return tmp;
    }
    self& operator--() { 
        node = (link_type)((*node).prev);
        return *this;
    }
    self operator--(int) { 
        self tmp = *this;
        --*this;
        return tmp;
    }
}
```

![](./img/hj_22.png)

list中对++进行重载时需要注意：

`self tmp = *this;//这是一个拷贝构造的过程，不会调用operator*()`

![](./img/hj_12.png)





# 算法

**算法其内部最终涉及元素的操作无非就是比大小**



sort和qsearch方法，要求其容器为randomIterator类型，因为算法中需要对两个迭代器进行相减操作。

以链表为基础实现的容器都不是randomIterator

队列的存储结构？



# 泛型编程技术

## template

```c++
template <...>;
//template里面可以是一个模板参数，也可以是一个具体类型(int),也可以是一个变量

// v是一个具体变量
template <class T, T v>
struct m_integral_constant
{
    static constexpr T value = v;
};
```



## 泛型编程常见库

### std::enable_if() 

enable_if 的主要作用就是当某个 condition 成立时，enable_if可以提供某种类型。enable_if在标准库中通过结构体模板实现的。

表现一种：编译期的分支逻辑。函数原型：

```c++
template<bool B, class T = void>
struct enable_if {};

template<class T>
struct enable_if<true, T> { typedef T type; };

//只有当第一个模板参数为true时，enable_if会包含一个type=T的公有成员，否则没有该公有成员。
```

> 使用场景

```c++
#include <iostream>
#include <type_traits>
 
// 1. the return type (bool) is only valid if T is an integral type:

template <class T>
typename std::enable_if<std::is_integral<T>::value,bool>::type
 is_odd (T i) {return bool(i%2);}
```

## trait技术

C++ 的 traits 技术，是一种约定俗称的技术方案，用来为同一类数据（包括自定义数据类型和内置数据类型）提供统一的操作函数，例如 advance(), swap(), encode()/decode() 等。

[理解trait技术](https://zhuanlan.zhihu.com/p/85809752)

[trait基础](https://zhuanlan.zhihu.com/p/413864991#:~:text=C%2B%2B%20%E7%9A%84%20traits%20%E6%8A%80%E6%9C%AF%EF%BC%8C%E6%98%AF%E4%B8%80%E7%A7%8D%E7%BA%A6%E5%AE%9A%E4%BF%97%E7%A7%B0%E7%9A%84%E6%8A%80%E6%9C%AF%E6%96%B9%E6%A1%88%EF%BC%8C%E7%94%A8%E6%9D%A5%E4%B8%BA%E5%90%8C%E4%B8%80%E7%B1%BB%E6%95%B0%E6%8D%AE%EF%BC%88%E5%8C%85%E6%8B%AC%E8%87%AA%E5%AE%9A%E4%B9%89%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B%E5%92%8C%E5%86%85%E7%BD%AE%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B%EF%BC%89%E6%8F%90%E4%BE%9B%E7%BB%9F%E4%B8%80%E7%9A%84%E6%93%8D%E4%BD%9C%E5%87%BD%E6%95%B0%EF%BC%8C%E4%BE%8B%E5%A6%82%20advance%20%28%29%2C,swap%20%28%29%2C%20encode%20%28%29%2Fdecode%20%28%29%20%E7%AD%89%E3%80%82)

## std::enable_if()

`std::enable_if` 顾名思义，满足条件时类型有效。作为选择类型的小工具，其广泛的应用在 C++ 的模板元编程（meta programming）中。它的定义也异常的简单：

```c++
template <bool, typename T=void>
struct enable_if {
};
 
template <typename T>
struct enable_if<true, T> {
  using type = T;
};

std::enable_if< (3 > 2)>::type* mypoint1 = nullptr;	//相当于 void *mypoint1 = nullptr
std::enable_if< (3 < 2)>::type* mypoint1 = nullptr;	//参数为false，没有type类型，编译报错
```

由上可知：

- 可接受两个参数，只有当第一个参数为true时，type才有定义。
- 可接受一个参数，当参数为true时，type才有定义，为void

### std::enable_if()的引用原因

enable_if()的出现体现c++的SFINAE规则，所谓的***SFINAE规则***就是在编译时进行查找替换，对于重载的函数，如果能够找到合适的就会替换，如果第一个不合适并不会报错，而会使用下一个替换直到最后一个，如果都不满足要求，那么才会报错。出现二义性的话也会报错。

### std::enable_if()的用法

- 用法一: 类型偏特化

在使用模板编程时，经常会用到根据模板参数的某些特性进行不同类型的选择，或者在编译时校验模板参数的某些特性

```c++
template <typename T, typename Enable=void>
struct check{};
 
template <typename T>
struct check<T, typename std::enable_if<T::value>::type> {
  static constexpr bool value = T::value;
}; 
```

上述的check会根据传入的类型T进行判断：T为true执行下面的check，反之执行上面的。

- 用法二: 控制函数返回类型

对于模板函数，有时希望根据不同的模板参数返回不同类型的值，进而给函数模板也赋予类型模板特化的性质.

由于[函数模板不能偏特化](http://www.gotw.ca/publications/mill17.htm)，通过 `enable_if` 便可以根据 `k` 值的不同情况选择调用哪个 `get`，进而实现函数模板的多态。

```c++
template <std::size_t k, class T, class... Ts>
typename std::enable_if<k==0, typename element_type_holder<0, T, Ts...>::type&>::type
get(tuple<T, Ts...> &t) {
  return t.tail; 
}
 
template <std::size_t k, class T, class... Ts>
typename std::enable_if<k!=0, typename element_type_holder<k, T, Ts...>::type&>::type
get(tuple<T, Ts...> &t) {
  tuple<Ts...> &base = t;
  return get<k-1>(base); 
}

get<i>(t);

```

- 用法三：校验函数模板参数类型

一个通过返回值，一个通过默认模板参数，都可以实现校验模板参数是整型的功能。

函数模板不能偏特化，所以检验函数参数类型时需要`typename = typename std::enable_if<>`或者`typename std::enable_if<!t, int>::type=0`

```c++
template <typename T>
typename std::enable_if<std::is_integral<T>::value, bool>::type
is_odd(T t) {
  return bool(t%2);
}
 
template <typename T, typename = typename std::enable_if<std::is_integral<T>::value>::type>
bool is_even(T t) {
  return !is_odd(t); 
}

// 根据类型选择check
template <bool t, class T, typename = typename std::enable_if<!t>::type>
void check(T value)
{
    std::cout << "false" << std::endl;
}
 

template<bool t, class T, typename = typename std::enable_if<t>::type> 
void check_true(T value)
{
    std::cout << "true" << std::endl;
}

int main()
{
    int i = 1;
    check_true<true>(i);
    check<false>(i);
    system("pause");
    return 0;
}

// 第二种方式
template <bool t, class T, typename std::enable_if<!t, int>::type=0>
void check(T value)
{
    std::cout << "false" << std::endl;
}
 

template<bool t, class T, typename std::enable_if<t, int>::type=0> 
void check_true(T value)
{
    std::cout << "true" << std::endl;
}

int main()
{
    int i = 1;
    check_true<true>(i);
    check<false>(i);
    system("pause");
    return 0;
}
```

