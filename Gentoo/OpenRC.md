# OpenRC Installation

Adjust your timezone data. First, see your time zone (3 first lines) and then update the timezone (last lines):
```bash
ls /usr/share/zoneinfo
ls /usr/share/zoneinfo/yourtimezone
echo "Europe/Madrid" > /etc/timezone # This is an example, check yours with 'ls /usr/share/zoneinfo/yourtimezone'
emerge --config sys-libs/timezone-data
```

Set up locations in this file: 
```bash
nano -w /etc/locale.gen
```

Write the following lines in the file, for example if your language is spanish (Check yours in the Gentoo Handbook if you want. American is default):
<pre>
es_ES ISO-8859-1
es_ES.UTF-8 UTF-8
</pre>

Update the configuration you just made. In the case that es_ES.utf8 or your language is 6:
```bash
locale-gen
eselect locale list
eselect locale set 9 #Change 9 to the number of the desired locale
```

Recharge the enviroment:
```bash
env-update && source /etc/profile && export PS1="(chroot) ${PS1}"
```

## KERNEL CONFIGURATION / INSTALLATION
At this point, you can install the default Linux kernel or customize your kernel.
- <a href="https://github.com/Filipondios/Gentoo/blob/main/Gentoo%20Installation/DefaultKernel-Installation.md">Default Kernel Installation</a>
- <a href="https://github.com/Filipondios/Gentoo/blob/main/Gentoo%20Installation/CustomKernel-Installation.md">Custom Kernel Installation</a>

## SYSTEM CONFIGURATION

Edit the FSTAB file (which defines the partitions that we have):
```bash
nano -w /etc/fstab
```

Write taking advantage of the commented lines and delete the previously created line:
<pre>
/dev/sda1    /boot    ext2      defaults           0 2
/dev/sda2    none     swap	sw	           0 0	
/dev/sda3    /        ext4	noatime            0 1
/dev/cdrom   /mnt     /cdrom	auto noauto,ro     0 0
</pre>

Keep uncommented this lines ONLY. All spaces are Tabs execpt the ones between the last two numbers in each row.

Network configuration. Write the desired name for your host in this file:
```bash
nano -w /etc/conf. d/hostname
hostname="Filipondios" # Example
```

Download the necessary tools for the Internet configuration:
```bash
emerge --ask --noreplace net-misc/netifrc
```

Now, see your network interface with 'ip addr' (its not lo <LOOPBACK>, the second one):
```bash
ip addr 
```

Write in the following file your network interface:
```bash
nano -w /etc/conf.d/net
config_yournetworkinterafce="dhcp"    # example: config_eth0="dhcp"
```

Finish the network configuration:
```bash
cd /etc/init.d
ln -s net.lo net.yournetworkinterafce
rc-update add net.yournetworkinterafce default
```

Now you must edit the Hosts file:
```bash
nano -w /etc/hosts
```

Write taking advantage of the uncommented lines (left). For example, in my case (right):
<pre>
127.0.0.1	yourhostname	localhost         # 127.0.0.1	Filipondios   localhost
::1		yourhostname	localhost         # ::1		Filipondios   localhost
</pre>

Set a password for the superuser (root):
```bash
passwd
```

Change keymap="us" to keymap="es" (Spanish) or your language
```bash
nano -w /etc/conf. d/keymaps
```

Finally, see if clock="UTC" if later it works wrong, change it to clock="local":
```bash
nano -w /etc/conf.d/hwclock
```

## SYSTEM TOOLS

Intall the system logger:
```bash
emerge --ask app-admin/sysklogd
rc-update add sysklogd default
```

Finally install the network manager:
```bash
emerge --ask net-misc/dhcpcd #FOR LAN
emerge -ask net-wireless/iw net-wireless/wpa_supplicant # For WIFI
```
 
If you have a ext2 or ext4 file format in your root partition and a boot partition with fat32, you should install this packages:
```bash
emerge -av sys-fs/e2fsprogs sys-fs/dosfstools
```
Anyways, if you have another file system format, you should look in the <a href="https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/Tools">Gentoo Handbook</a> your correct package to install, so you
can optimize your file-system.
 
<b>NOW GO BACK TO THE <a href="https://github.com/Filipondios/Gentoo/tree/main/Gentoo%20Installation">ORIGINAL GUIDE</a></b>
