# Gentoo Default Kernel Installation

Start configuring the system before installing the default Linux Kernel
```bash
emerge --ask sys-kernel/gentoo-sources
eselect kernel list
eselect kernel set kernelnumber
ls -l /usr/src/linux
```

Install the default Kernel
```bash
echo "sys-kernel/linux-firmware @BINARY-REDISTRIBUTABLE" | tee -a /etc/portage//package.license
emerge --ask sys-kernel/genkernel
genkernel all
ls /boot/vmlinu* /boot/initramfs*
```

Finally, install the Linux Firmware
```bash
emerge --ask sys-kernel/linux-firmware
```
<b>NOW GO BACK TO THE <a href="https://github.com/Filipondios/Gentoo/blob/main/Gentoo%20Installation/OpenRC.md">OPENRC</a> OR THE <a href="https://github.com/Filipondios/Gentoo/blob/main/Gentoo%20Installation/Systemd.md">SYSTEMD</a> INSTALLATION GUIDE</b>
