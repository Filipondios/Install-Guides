# Gentoo Default Installation - No Kernel Customization - Systemd 

Adjust your timezone data. First, see your time zone (3 first lines) and then update the timezone (last lines):
```bash
ls /usr/share/zoneinfo                #Check your timezone
ls /usr/share/zoneinfo/yourtimezone   #Check your timezone
ln -sf ../usr/share/zoneinfo/Europe/Brussels /etc/localtime #Change Europe/Brussels to your timezone
```

Now that we have our timezone data, we should configure our locale:
```bash
nano -w /etc/locale.gen
```

Write the following lines in the file, for example if your language is spanish (Check yours in the Gentoo Handbook if you want. American is default):
<pre>
es_ES ISO-8859-1
es_ES.UTF-8 UTF-8
</pre>

Its recomendable to choose the UTF-8 option, so lets make it with the following commands:
```bash
locale-gen
eselect locale list
eselect locale set 9 #Change 9 to the number of the desired locale
```

Now lets make Systemd configurations:
```bash
ln -sf /proc/self/mounts /etc/mtab
```

Reload the enviroment:
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


Network configuration. Write the desired name for your host in this file:
```bash
touch /etc/hostnames && echo "Filipondios" > /etc/hostnames # Chage Filipondios to your desired hostname
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

Now lets make some Systemd configs:
<pre>
systemd-machine-id-setup
nano /etc/systemd/network/50-dhcp.network
</pre>

Now write in the file the next config:
```bash
[Match]
Name=en*
 
[Network]
DHCP=yes
```

Now lets enable the network service for Systemd
```bash
systemctl enable systemd-networkd.service
systemctl preset-all
```

Lets set our root password:
```bash
passwd
```

If you have a ext2 or ext4 file format in your root partition and a boot partition with fat32, you should install this packages:
```bash
emerge -av sys-fs/e2fsprogs sys-fs/dosfstools
```
Anyways, if you have another file system format, you should look in the <a href="https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/Tools">Gentoo Handbook</a> your correct package to install.

Install the network manager:
```bash
emerge --ask net-misc/dhcpcd #FOR LAN
emerge -ask net-wireless/iw net-wireless/wpa_supplicant # For WIFI
```

<b>NOW GO BACK TO THE <a href="https://github.com/Filipondios/Gentoo/tree/main/Gentoo%20Installation">ORIGINAL GUIDE</a></b>
