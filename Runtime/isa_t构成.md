---
    
title: iOS isa_t构成

date: 2020-04-15 16:23:56

cover: /img/girl037.webp

---



### 1、结构

```C
union isa_t 
{
    isa_t() { }
    isa_t(uintptr_t value) : bits(value) { }

    Class cls;
    uintptr_t bits;
    struct {
        uintptr_t nonpointer        : 1; // 是否Tagged Poniter
        uintptr_t has_assoc         : 1; // 是否有关联引用
        uintptr_t has_cxx_dtor      : 1; // 是否有析构器
        uintptr_t shiftcls          : 33; // 类的指针 MACH_VM_MAX_ADDRESS 0x1000000000
        uintptr_t magic             : 6; // 是否初始化完成
        uintptr_t weakly_referenced : 1; // 是否被弱引用
        uintptr_t deallocating      : 1; // 是否正在释放内存
        uintptr_t has_sidetable_rc  : 1; // 引用计数是否过大
        uintptr_t extra_rc          : 19; // 引用计数
    };
} 
```



