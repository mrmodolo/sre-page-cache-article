# Essential Page Cache theory

First of all, let’s start with a bunch of reasonable questions about Page Cache:

- What is the **Linux Page Cache**?
- What problems does it solve?
- Why do we call it «**Page**» Cache ?

In essence, the Page Cache is a part of the Virtual File System ([VFS](https://en.wikipedia.org/wiki/Virtual_file_system))
whose primary purpose, as you can guess, is improving the IO latency of read and write operations.
A write-back cache algorithm is a core building block of the Page Cache.

---
**NOTE**

If you’re curious about the write-back algorithm (and you should be),
it’s well described on [Wikipedia](https://en.wikipedia.org/wiki/Cache_%28computing%29#Writing_policies),
and I encourage you to read it or at least look at the figure with a 
flow chart and its main operations.

---

“Page” in the Page Cache means that linux kernel works with memory units called
pages. It would be cumbersome and hard to track and manage bites or even bits of
information. So instead, Linux’s approach (and not only Linux’s, by the way) is
to use pages (**usually 4K in length**) in almost all structures and operations.
Hence the minimal unit of storage in Page Cache is a page, and it doesn’t matter
how much data you want to read or write. All file IO requests are aligned to
some number of pages.

The above leads to the important fact that **if your write is smaller than the
page size, the kernel will read the entire page before your write can be
finished**.

The following figure shows a bird’s-eye view of the essential Page Cache
operations. I broke them down into reads and writes.

![Page Cache](page-cache.png)

