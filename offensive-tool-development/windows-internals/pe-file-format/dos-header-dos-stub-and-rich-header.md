# DOS Header, DOS Stub, & Rich Header

## Introduction

TLDR: The DOS Header is the most important part in this page, and within that the key takeaway is the use of `e_lfanew` to access the next header: `NT Headers`.

## DOS Header

### Overview

The DOS Header (also known as MS-DOS header) is a 64 byte long structure that exists at the start of a PE file. It contains the DOS magic bytes `4D 5A` "MZ" in ascii. It is not important for the functionality of modern windows. It is however important on MS-DOS. But we don't need to worry about that.

### Structure

```c
typedef struct _IMAGE_DOS_HEADER {      // DOS .EXE header
    WORD   e_magic;                     // Magic number
    WORD   e_cblp;                      // Bytes on last page of file
    WORD   e_cp;                        // Pages in file
    WORD   e_crlc;                      // Relocations
    WORD   e_cparhdr;                   // Size of header in paragraphs
    WORD   e_minalloc;                  // Minimum extra paragraphs needed
    WORD   e_maxalloc;                  // Maximum extra paragraphs needed
    WORD   e_ss;                        // Initial (relative) SS value
    WORD   e_sp;                        // Initial SP value
    WORD   e_csum;                      // Checksum
    WORD   e_ip;                        // Initial IP value
    WORD   e_cs;                        // Initial (relative) CS value
    WORD   e_lfarlc;                    // File address of relocation table
    WORD   e_ovno;                      // Overlay number
    WORD   e_res[4];                    // Reserved words
    WORD   e_oemid;                     // OEM identifier (for e_oeminfo)
    WORD   e_oeminfo;                   // OEM information; e_oemid specific
    WORD   e_res2[10];                  // Reserved words
    LONG   e_lfanew;                    // File address of new exe header
  } IMAGE_DOS_HEADER, *PIMAGE_DOS_HEADER;
```

The most important members are:

*   #### <mark style="color:yellow;">e\_lfanew</mark> - Holds the offset to the start of NT headers. It tells the loader where to look for the NT header as well as allows us to access the NT header.


* #### &#x20;e\_magic - This is the magic bytes `4D 5A` "MZ" in ascii. It is the DOS family signature.

#### Here is the view of DOS Header in PE Bear

<mark style="color:red;">**NOTE:**</mark> The offset to `e_lfanew` is 0x3C from the start of the DOS Header.

<figure><img src="../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## DOS Stub

### Overview

The DOS Stub is an error message that says “This program cannot be run in DOS mode. That's all.&#x20;

## Rich Header

The Rich Header is an undocumented structure that lies between the DOS Stub and the start of NT Headers. <mark style="color:yellow;">**It is included in executables compiled with Visual Studio Toolset.**</mark>

It technically is not part of the PE structure, and can be zeroed out. However, there have been numerous threat actors and malware writers that have utilized this Rich Header&#x20;
