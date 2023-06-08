# Prepare environment for experiments

Before starting, I want to be on the same page with the reader so
that any example or code snippet can be executed, compiled, and checked.
Therefore we need a modern GNU/Linux installation to play with code and kernel.

If you are using Windows or Mac OS, I would suggest installing Vagrant with 
Virtual Box. For the GNU/Linux distributive, I’d like to use Arch Linux.
Arch is a good example of an actual modern version of the GNU/Linux 
system (BTW, I use Arch Linux). It supports the latest kernels, systemd and cgroup v2.

If you’re already on Linux, you know what to do 😉.

**Can I use docker?**

---
**NOTE**

Unfortunately, no. We need a system where we can go nuts and play 
around with cgroup limits, debug programs with low-level tools 
and run code as a root user without any limitations.

---

So below, I’m showing all you need to install on Arch.

## Arch Linux provisioning

When you get your Arch running, please update it and install the following packages:

```bash
sudo pacman -Sy git base-devel go
```

We need to install yay (https://github.com/Jguer/yay) in order to be able to setup 
software from community-driven repositories:

```bash
cd ~
mkdir src
cd $_
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
```

Install vmtouch tool from aur:

```bash
yay -Sy vmtouch 
```

We will need page-type tool from the kernel repo, so the easiest way to install it 
is to download the linux kernel release and make it manually:


```bash
mkdir ~/src
cd $_
KERNEL="$(uname -r | sed 's/\([0-9].[0-9]\).*/\1/')"
wget https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/snapshot/linux-"${KERNEL}".tar.gz
tar -xzf linux-"${KERNEL}".tar.gz
cd linux-${KERNEL}/tools/mm
make
sudo make install
```
Now we are almost ready. We need to generate a test data file, which will be used in our experiments with Page Cache:

```bash
dd if=/dev/random of=/var/tmp/file1.db count=128 bs=1M  
```
And the final step is dropping all linux caches in order to get a clean box:

```bash
sync; echo 3 | sudo tee /proc/sys/vm/drop_caches
```

