---
title: std::enable_shared_from_this
layout: post
date: 2016-10-13 20:00:00 +07:00
author: ĐOÀN Trần Công Danh
categories: programming
tags: [cpp, smart-pointer]
---

I have had a hard time to understand why do we need
`std::enable_shared_from_this`. After reading [std::enable_shared_from_this][1],
I still wonder why we need to have `std::enable_shared_from_this` because the
example in that page call `getptr()` from a `std::shared_ptr`:

```cpp
#include <memory>
#include <iostream>
 
struct Good: std::enable_shared_from_this<Good>
{
    std::shared_ptr<Good> getptr() {
        return shared_from_this();
    }
};
 
struct Bad
{
    std::shared_ptr<Bad> getptr() {
        return std::shared_ptr<Bad>(this);
    }
    ~Bad() { std::cout << "Bad::~Bad() called\n"; }
};
 
int main()
{
    // Good: the two shared_ptr's share the same object
    std::shared_ptr<Good> gp1(new Good);
    std::shared_ptr<Good> gp2 = gp1->getptr();
    std::cout << "gp2.use_count() = " << gp2.use_count() << '\n';
 
    // Bad, each shared_ptr thinks it's the only owner of the object
    std::shared_ptr<Bad> bp1(new Bad);
    std::shared_ptr<Bad> bp2 = bp1->getptr();
    std::cout << "bp2.use_count() = " << bp2.use_count() << '\n';
} // UB: double-delete of Bad
```

At this very moment, I realise that it's used to get the `std::shared_ptr` from
the raw pointer which is passed around some where like this:


```cpp
#include <memory>
#include <iostream>
 
struct Good: std::enable_shared_from_this<Good>
{
    std::shared_ptr<Good> getptr() {
        return shared_from_this();
    }
};
 
struct Bad
{
    std::shared_ptr<Bad> getptr() {
        return std::shared_ptr<Bad>(this);
    }
    ~Bad() { std::cout << "Bad::~Bad() called\n"; }
};

void foo(Good *gp) {
    std::shared_ptr<Good> ngp1 = gp->shared_from_this();
    std::shared_ptr<Good> ngp2 = gp->getptr();
    std::cout << "ngp2.use_count() = " << ngp2.use_count() << '\n';
}
 
int main()
{
    // Good: the two shared_ptr's share the same object
    std::shared_ptr<Good> gp1(new Good);
    std::shared_ptr<Good> gp2 = gp1->getptr();
    foo(gp1.get());
    std::cout << "gp2.use_count() = " << gp2.use_count() << '\n';
}
```


  [1]: http://en.cppreference.com/w/cpp/memory/enable_shared_from_this