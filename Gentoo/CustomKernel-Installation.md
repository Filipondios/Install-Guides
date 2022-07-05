# Gentoo Installation - Kernel Customization  

![imagen](https://user-images.githubusercontent.com/91225771/176941189-e06c6432-79ff-432b-a5ac-a68c026df8d6.png)

First, install the linux firmware:
```bash
echo "sys-kernel/linux-firmware @BINARY-REDISTRIBUTABLE" | tee -a /etc/portage//package.license
emerge --ask sys-kernel/linux-firmware
```

Now, we need to make a symlink to the kernel.
```bash
root #emerge --ask sys-kernel/gentoo-sources
eselect kernel list
eselect kernel set 1
ls -l /usr/src/linux
```

Now that we installed the firmware, lets customize, compile and install our linux kernel. First, we need to download
a Gentoo app that will help us to configure the kernel. So then:
```bash
emerge --ask sys-apps/pciutils
cd /usr/src/linux
make menuconfig
```

Now, a graphical menu should appear wich will allow us to customize the kernel. Configurin the kernel is not a easy
task, you have to know what are you doing or you'll have a lot of chances to fail and fail making a custom kernel,
having that the consecuences that it takes. 
</br>

---------

I hightly recommend you to see the <a href="https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/Kernel">Official Gentoo Guide</a>
and see whats going on. You can also see a <a href="https://www.youtube.com/watch?v=NVWVHiLx1sU">Youtube video</a> that i found that 
explains all the intesting features of the kernel. 

---------

<b>Now, lets set the objectives</b>. When we are trying to configure our kernel, we are selecting the features that we only 
need. For example: If there is a module or a feature for AMD processors and you have a Intel processor, you are 
going to deselect all the AMD features that are available. As I said before, take a look to the Gentoo Wiki and
the video, and then take your decissions.

When you finish, just type this commands:
```bash
make clean
make && make modules_install
ls -l arch/x86_64/boot/bzImage 
make install
```

<b>NOW GO BACK TO THE <a href="https://github.com/Filipondios/Gentoo/blob/main/Gentoo%20Installation/OpenRC.md">OPENRC</a> OR THE <a href="https://github.com/Filipondios/Gentoo/blob/main/Gentoo%20Installation/Systemd.md">SYSTEMD</a> INSTALLATION GUIDE</b>
