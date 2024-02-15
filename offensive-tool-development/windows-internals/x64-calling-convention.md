# x64 Calling Convention

## Introduction

A calling convention is an implementation scheme, or consensus, for how functions receive parameters from their caller, and how the return the result. Calling conventions are part of the _**Application Binary Interface (ABI)**_.

Arguments are strictly one to one with its its respective register. They do not spread across registers.

<mark style="color:yellow;">Any argument that doesn't fit in 8 bytes, or isn't 1, 2, 4, or 8 bytes, must be passed by reference.</mark>&#x20;

## \_\_fastcall

64-bit Windows uses the fast-call (`__fastcall`) calling convention. It operates using registers for the first four arguments, and the stack frame for any additional arguments.

* The first four integer or pointer arguments (if they exist) are passed through the registers `RCX`, `RDX`, `R8`, and `R9`.
* Floating arguments are passed `XMM0L`, `XMM1L`, `XMM2L`, and `XMM3L`.
* 16 byte arguments are passed by reference.

```c
func1(int a, int b, int c, int d, int e, int f);
// a in RCX, b in RDX, c in R8, d in R9, f then e pushed on stack
```

See [MASM Assembly passing parameters](../../programming/masm-assembly.md#passing-parameters) for more information on the registers and it's assembly. Along with [Microsoft's register usage](https://learn.microsoft.com/en-us/cpp/build/x64-software-conventions?view=msvc-170#x64-register-usage).

#### Return Value

A scalar return value that can fit into 64 bits, including the **`__m64`** type, is returned through RAX. Non-scalar types including floats, doubles, and vector types such as [`__m128`](https://learn.microsoft.com/en-us/cpp/cpp/m128?view=msvc-170), [`__m128i`](https://learn.microsoft.com/en-us/cpp/cpp/m128i?view=msvc-170), [`__m128d`](https://learn.microsoft.com/en-us/cpp/cpp/m128d?view=msvc-170) are returned in XMM0.&#x20;



## \_\_vectorcall

Vector-call (\_\_vectorcall) calling convention specifies that arguments are to be passed in registers, when possible. \_\_vectorcall uses more registers for arguments than \_\_fastcall

{% embed url="https://learn.microsoft.com/en-us/cpp/cpp/vectorcall?view=msvc-170" %}



## \_\_**cdecl**

**`__cdecl`** is the default calling convention for C and C++ programs. Because the stack is cleaned up by the caller, it can do `vararg` functions.&#x20;

| Element                          | Implementation                                                                                                   |
| -------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| Argument-passing order           | Right to left.                                                                                                   |
| Stack-maintenance responsibility | Calling function pops the arguments from the stack.                                                              |
| Name-decoration convention       | Underscore character (\_) is prefixed to names, except when \_\_cdecl functions that use C linkage are exported. |
| Case-translation convention      | No case translation performed.                                                                                   |

**Example**

```cpp
// Example of the __cdecl keyword on function
int __cdecl system(const char *);
// Example of the __cdecl keyword on function pointer
typedef BOOL (__cdecl *funcname_ptr)(void * arg1, const char * arg2, DWORD flags, ...);
```

##

## \_\_stdcall

The \_\_stdcall calling convention is used to call Win32 API functions. The callee cleans the stack and the compiler makes `vararg` functions `__cdecl`

| Element                          | Implementation                                                                                                                                                                                                                                                  |
| -------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Argument-passing order           | Right to left.                                                                                                                                                                                                                                                  |
| Argument-passing convention      | By value, unless a pointer or reference type is passed.                                                                                                                                                                                                         |
| Stack-maintenance responsibility | Called function pops its own arguments from the stack.                                                                                                                                                                                                          |
| Name-decoration convention       | An underscore (`_`) is prefixed to the name. The name is followed by the at sign (`@`) followed by the number of bytes (in decimal) in the argument list. Therefore, the function declared as `int func( int a, double b )` is decorated as follows: `_func@12` |
| Case-translation convention      | None                                                                                                                                                                                                                                                            |

**Example**

```c
// Example of the __stdcall keyword
#define WINAPI __stdcall
// Example of the __stdcall keyword on function pointer
typedef BOOL (__stdcall *funcname_ptr)(void * arg1, const char * arg2, DWORD flags, ...);
```



## \_\_declspec



## References

{% embed url="https://en.wikipedia.org/wiki/Calling_convention" %}

{% embed url="https://learn.microsoft.com/en-us/cpp/build/x64-calling-convention?view=msvc-170" %}

{% embed url="https://learn.microsoft.com/en-us/cpp/cpp/argument-passing-and-naming-conventions?view=msvc-170" %}
