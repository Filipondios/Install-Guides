# Gentoo Installation: OpenRC, Systemd - Default and Custom Kernel

---------

### First of all...
- This is my personal Gentoo install guide, you can always add some features or configurations if you have the experience to
do it. If you are not sure of what are you doing, keep an eye on the <a href="https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/About">Gentoo Handbook</a>
and this guide.
- This guide works like this: There are a lot of commands that are common to all the installations, so there will be a moment where
you'll have to select the installation guide that you prefer. 

---------

## START
Let's start. First, see if you have connection to the Internet:

```bash
ping -c 3 google.com
```

In this guide we wont solve any internet problems because there are so many out there, so the best thing you can do if you have an error is reading 
the Gentoo Handbook.

## PREPARE THE DISKS

See the disk partitions

```bash
lsblk
```

Now there are two options
1) fdisk /dev/sda (console interface)
2) cfdisk (more graphical interface)

In both cases, we must know if our system is BIOS or UEFI. In the case of BIOS, we'll choose the partition system DOS and in case of UEFI we will choose GPT.
In addition, the scheme will be the same in both cases. Personally, this is my favourite partition table, because I've tried a lot of combinations in many 
different Linux distributions and in all all of them, this scheme was the one that had the best performance and distribution of disk space:

<br>
<table>
  <tr>
    <th>Partition</th>
    <th>File System</th>
    <th>Size</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>/dev/sda1</td>
    <td>fat32(UEFI) or ext2 (BIOS)</td>
    <td>256Mb</td>
    <td>Boot/Efi Partition</td>
  </tr>
  <tr>
    <td>/dev/sda2</td>
    <td>Swap</td>
    <td>RAM*2</td>
    <td>Swap Partition</td>
  </tr>
  <tr>
    <td>/dev/sda3</td>
    <td>ext4</td>
    <td>Rest of the Disk</td>
    <td>Root Partition</td>
  </tr>
</table> 

</br>
<b>IMPORTANT: IN THIS GUIDE WILL BE ONLY SPECIFIED THE INSTALLATION WITH BIOS)</b>
</br>
Before doing nothing, if you have another SOs in you computer, check with the "lsblk" command in what number of partition
this SOs ends. Then we will do the process bellow but instead of beggining with 1, you'll start with the next of your partitions. Example:
</br></br>

<table>
  <tr>
    <th>Partition</th>
    <th>Description</th>
  </tr>
  <tr>
    <td>/dev/sda1</td>
    <td>Windows boot partition / EFI</td>
  </tr>
  <tr>
    <td>/dev/sda2</td>
    <td>Windows Storage</td>
  </tr>
  <tr>
    <td>/dev/sda3</td>
    <td>Free space</td>
  </tr>
</table> 

</br>
<b>In this case, you must start with 3 instead if 1 in the first step bellow, the second 4, and so on.</b>

We return to the installation, and if you choose fdisk /dev/sda:

<pre>
-> Bios=o; UEFI=g

-> n                #
-> p                # Our BIOS partition size will be 256Mb and we wont change that, because in the Gentoo Guide
-> 1                # because the Gentoo Handbook says that 256Mb its the optimal size.
-> ENTER            #
-> +256M            #

-> n                #
-> p                # This will be our swap partition. You can change the value "8" by "4" or "2" if you dont
-> 2                # have enough space. Although the Gentoo Handbook says that this partition have to be RAM*2,
-> ENTER            # I've tried with those disks spaces but i see no difference in the computer performance.
-> +8G              #

-> n                #
-> p                # This will be our root partition or "/". In this partition, if you make a double <ENTER> like 
-> 3                # i did, the root partition will fill up the rest of the disk space. If you want to leave more  
-> ENTER            # space for other SOs, just type the disk space that you want instead of the last ENTER.
-> ENTER            #

-> t                #
-> 2                # This time we will be specifiying what type of partition is going to be the second one, for us, 
-> L                # its going to be swap. IMPORTANT: See carefully if the number 82 its the swap partition, it 
-> 82               # could be changed.

-> a
-> 1

-> w
</pre>

If you choose cfdisk, select BIOS or UEFI in the first menu and do the same partition scheme. Finally select w for write the changes.

Finish partitioning

```bash
mkfs.ext2 /dev/sda1 (BIOS) 
mkswap /dev/sda2
swapon /dev/sda2
mkfs.ext4 /dev/sda3
mount /dev/sda3 /mnt/gentoo
```

Adjust day/time
```bash
date MMDDhhmmAAAA (Month, Day, Hour, Minute and Year) #example for 01/01/2022 at 12:00 : date 010112002022  
```
## INSTALL STAGE3

```bash
cd /mnt/gentoo
```

With a program called links, we are going to move with the keyboard arrows, and select the Stage 3 tarball. Once we have downloaded it, we exit the program with scape + select exit in the top menu.

```bash
links https://www.gentoo.org/downloads                                          
-> ENTER                                                                     
-> ENTER                                                                     
-> Select Stage 3 Systemd or OpenRc                                                   
-> SAVE                                                                      
tar xpvf vernombrearchivo.tar.xz --xattrs-include='*.*' --numeric-owner       
```

## CFLAGS and CXXFLAGS
```bash
vi /mnt/gentoo/etc/portage/make.conf
```

#### IMPORTANT: Look at processor type and brand and look at the following page:
<a href="https://wiki.gentoo.org/wiki/Safe_CFLAGS#Skylake.2C_Kaby_Lake.2C_Kaby_Lake_R.2C_Coffee_Lake.C_Comet_Lake">Gentoo Wiki -  Safe CFLAGS</a>

#### In the case of Intel, its pretty easy. Search your processor and code name. In my case I have to put the following since my processor is Intel and its code name is Kaby Lake:

Replace in the COMMON_FLAGS variable (IN MY CASE):
```bash
COMMON_FLAGS="-march=skylake -O2 -pipe"
```

Write at the end of the document:
```bash
MAKEOPTS="-jnumberofcores" # For example, if you have 2 cores:  MAKEOPTS="-j2"
```

Save and exit the file with the keybanding scape + :wq

## INSTALL THE BASE SYSTEM

Select the server that is nearest to your country with the space key and exit saving changes.
```bash
mirrorselect -i -o >> /mnt/gentoo/etc/portage/make.conf
mkdir --parents /mnt/gentoo/etc/portage/repos.conf
cp /mnt/gentoo/usr/share/portage/config/repos.conf /mnt/gentoo/etc/portage/repos.conf/gentoo.conf
```

See that everything is copied
```bash
cat /mnt/gentoo/etc/portage/repos.conf/gentoo.conf
```

Copy DNS information
```bash
cp --dereference /etc/resolv.conf /mnt/gentoo/etc/
```

Mount the necessary Files
```bash
mount --types proc /proc /mnt/gentoo/proc
mount --rbind /sys /mnt/gentoo/sys
mount --make-rslave /mnt/gentoo/sys
mount --rbind /dev /mnt/gentoo/dev
mount --make-rslave /mnt/gentoo/dev
mount --bind /run /mnt/gentoo/run
mount --make-slave /mnt/gentoo/run 
```

Enter the new enviroment
```bash
chroot /mnt/gentoo /bin/bash
source /etc/profile
export PS1="(chroot) ${PS1}"
```

Mount the boot partition ( We never did it before. We only mounted the root and swap partition).
```bash
mount /dev/sda1 /boot
```

Update the set @WORLD
```bash
emerge-webrsync
emerge --ask --verbose --update --deep --newuse @world
```

Now, select what init system do you want. I hightly recomend you <b>OpenRC</b>. Why? YES. Do it.
- <a href="https://github.com/Filipondios/Gentoo/blob/main/Gentoo%20Installation/OpenRC.md">OpenRC Istallation</a>
- <a href="https://github.com/Filipondios/Gentoo/blob/main/Gentoo%20Installation/DefaultKernel-Systemd.md">Systemd Installation</a>

## INSTALL THE BOOT MANAGER

There are a lot of boot managers out there, but the one i most reccomend is Grub because its 
very complete. So then, lets install GRUB2:
```bash
emerge --ask --verbose sys-boot/grub:2
grub-install /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg
```

## FINISH THE INSTALLATION

```bash
exit
cd
umount -l /mnt/gentoo/dev{/shm,/pts,}
umount -R /mnt/gentoo
reboot
```

If at this point the system rebooted and GRUB showed a menu with Gentoo as an option, you
finished the installation and you can be shure that everything will work nice. Now, we just login
with the root user and the password we created before, thats because we didnt create a normal user, but 
you can do it later. 
As I said, login with this info:
<pre>
User: root / The user you created
Password: The password you set before
</pre>

Now you are ready to use Gentoo, now you can create a new user, intall a desktop manager or a window manager and then install all the graphical aplications that are available and you want.
