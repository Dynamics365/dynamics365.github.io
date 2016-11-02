---
title: C Restrict Pointer
layout: post
date: 2016-08-23 23:45:00 +07:00
categories: programming
tags: [c-language, pointer]
---

Let's examine this function:

```c
int foo(int *a, int *b) {
  *a = 6;
  *b = 7;
  return *a * *b;
}
```

Question! When we call `foo(a_pointer_to_int, another_pointer_to_int)`, can you
guess the result?

Obviously, most of us answer `foo` always returns `42`, but what happens if `a`
and `b` point to the same `int` object? Yeah, in that case, `foo` will return
`49`.

Now, you can see that we have a major problem with optimization here, if `a`
and `b` is guaranteed to point to difference `int` object, the above code can be
optimized as:

```c
int foo(int *a, int *b) {
  *a = 6;
  *b = 7;
  return 42;
}
```

Which is, obviously, faster!

Because of this problem, the keyword `restrict` steps in.
The `restrict` keyword is a declaration of intent given by the programmer to the
compiler.
From <http://en.cppreference.com/w/c/language/restrict>:

> During each execution of a block in which a restricted pointer P is declared
(typically each execution of a function body in which P is a function
parameter), if some object that is accessible through P (directly or indirectly)
is modified, by any means, then all accesses to that object (both reads and
writes) in that block must occur through P (directly or indirectly),
otherwise the behavior is undefined

It means that if any object is accessible through a pointer passed to that
function (directly or indirectly), it is not accessible through other pointer
passed to the function in question.

In this example:

```c
int foo(int * restrict a, int * restrict b) {
  *a = 6;
  *b = 7;
  return *a * *b;
}
```
The programmers promise to compiler that `a` and `b` will point to different
object of `int` when they call `foo`. Thus, it can be optimized as:

```c
int foo(int * restrict a, int * restrict b) {
  *a = 6;
  *b = 7;
  return 42;
}
```
In this example:

```c
char* char_cpy(char * restrict dest, char * restrict src, size_t size);
```
The programmers promise to compiler that `dest` and `src` won't intersect
with each other when they call `char_cpy`. It means that `[dest, dest + size)`
and `[src, src + size)` won't intersect with each other. Thus, that `char_cpy`
can be safely implemented as:

```c
char* char_cpy(char * restrict dest, char * restrict src, size_t size) {
  while (size--) {
    *dest++ = *src++;
  }
}
```
And compilers have their free to optimize that above code. Without `restrict`, we need
to do a lot of stuff to make this function work correctly.

In this example (although this is C++ with an extension for restrict, it work
mostly the same with C version): <https://godbolt.org/g/MzR5QB>, you can see
how much the compiler can optimize the output assembly (4/5).

### NOTE
Programmer (not compiler) must ensure that those pointers must not be aliased
(intersect) with other one. If not, the behavior is undefined.
