# SRE deep dive into Linux Page Cache 

In this series of articles, I would like to talk about **Linux Page Cache**. I believe
that the following knowledge of the theory and tools is **essential and crucial
for every SRE**. This understanding can help both in usual and routine everyday
DevOps-like tasks and in emergency debugging and firefighting. Page Cache is
often left unattended, and its better understanding leads to the following:

- more **precise capacity planning** and **container limit calculations**; 
- better **debugging and investigation skills** for memory and disk intensive 
  applications such as **database management system** and file sharing **storages**; 
- building safe and predictable runtimes for memory and/or IO-bound ad-hoc 
  tasks (for instance: backups and restore scripts, rsync one-liners, etc.).

I’ll display what utils
you should keep in mind when you’re dealing with Page Cache related tasks and
problems, how to use them properly to understand real memory usage, and how to
reveal issues with them. I will try to give you some examples of using these
tools that are close to real life situations. Here are some of these tools I’m
talking about below: vmtouch, perf, cgtouch, strace , sar and page-type.

Also, as the title says, “deep dive”, the internals of these utils will be shown
with an emphasis on the Page Cache stats, events, syscalls and kernel
interfaces. Here are some examples of what I’m touching on in the following
post:

- procfs files: `/proc/PID/smaps`, `/proc/pid/pagemap`, `/proc/kpageflags`,
  `/proc/kpagecgroup` and `sysfs` file: `/sys/kernel/mm/page_idle`;
- system calls: `mincore()`, `mmap()`, `fsync()`, `msync()`, `posix_fadvise()`,
  `madvise()` and others;
- different open and advise flags `O_SYNC`, `FADV_DONTNEED`, `POSIX_FADV_RANDOM`,
  `MADV_DONTNEED`, etc.

I’ll try to be as verbose as possible with simple (almost
all the way) code examples in Python, Go and a tiny bit of C.

And finally, any conversations about modern GNU/Linux systems can’t be fully
conducted without touching the **cgroup** (v2 in our case) and the **systemd** topics.
I’ll show you **how to leverage them** to get the most out of the systems, build
reliable, well-observed, controlled services, and sleep well at night while
on-call.

Readers should be confident if they have middle GNU/Linux knowledge and basic
programming skills.

All code examples larger than 5 lines can be found on github:
https://github.com/brk0v/sre-page-cache-article.
