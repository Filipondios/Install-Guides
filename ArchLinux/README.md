# ARCH-LINUX MANUAL INSTALLATION

## INITIAL CONFIGURATION
The first thing is load our keyboard layout (Mine is spanish. English=en)
```Bash
loadkeys es
```

Now we are going to try our configuration for WIFI, but if you want to use LAN or you are on VirtualBox, you can skip this step and go to DISK CONFIGURATION.

Let’s now check that the device is connected. You can check if the device has picked up an ip address with the following command, that should say that your network interface is UP (Its not a interface called lo or loopback):
```Bash
ip addr
```
You could also ping a site on the internet that you know accepts icmp requests (google being one of them).
```Bash
ping -c 3 google.com # You can also run ping -c 3 8.8.8.8
```
Then, we execute the following command:
```Bash
ip link set enp0s3 up # In my case
```
IMPORTANT: IF YOU HAD AN ERROR WITH THIS COMMAND, PLEASE CHECK <a href="https://github.com/Filipondios/Install-Guides/blob/main/ArchLinux/WifiError.md">MY SOLUTION</a>.

```Bash
iwlist enp0s3 scan # Maybe its not supported
wpa_passphrase netName netPassword > /etc/wifi
cat /etc/wifi
wpa_supplicant –B –i enp0s3 –D wext –c /etc/wifi
dhclient
```

## DISK CONFIGURATION
As always in a SO installation, one of the first things is checking our disks, make the partitions that we want and give a format to them. So then, we list our disks and their partitions wth the following command:
```Bash
lsblk
```
Now, we have to decide our partition scheme. As I always say, my favourite partition table that had the best performance and distribution of disk space was the following (Obviusly the numbers 1,2,3 can be different):

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
    <td>512Mb</td>
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

#### IMPORTANT: Before doing nothing, if you have another SOs in you computer, check with the "lsblk" command in what number of partition this SOs ends. Then we will do the process bellow but instead of beggining with 1, you'll start with the next of your partitions. Example:

<br>
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

#### In this case, you must start with 3 instead if 1 in the first step bellow, the second 4, and so on.

We return to the installation, and we run fdisk. WARNING: THIS GUIDE WILL ONLY SHOW THE INSTALLATION WITH EFI:

<pre>
fdisk

-> n                #
-> p                # Our EFI partition size will be 512Mb. In other installation guides like Gentoo, i said 256Mb
-> 1                # because the Gentoo Handbook said that 256Mb was the optimal size.Now, the Arch official guide
-> ENTER            # says that the optimal its 512Mb. As always, this depends on the SO.
-> +512M            #
-> t                #
-> ef               #

-> n                #
-> p                # This will be our swap partition. You can change the value "8" by "4" or "2" if you dont
-> 2                # have enough space. 
-> ENTER            # 
-> +8G              #
-> t                #
-> 2                #
-> 82               #

-> n                #
-> p                # This will be our root partition or "/". In this partition, if you make a double <ENTER> like 
-> 3                # i did, the root partition will fill up the rest of the disk space. If you want to leave more  
-> ENTER            # space for other SOs, just type the disk space that you want instead of the last ENTER.
-> ENTER            #
-> t                #  
-> 3                #
-> 83               #

-> w
</pre>

Finally, we give format the partitions we created:
```Bash
mkfs.fat -F32 /dev/sda1
mkswap /dev/sda2
mkfs.ext4 /dev/sda3
```
Now that we finally have formatted the partitions, let’s mount them:
```Bash
mount /dev/sda3 /mnt
swapon /dev/sda2
mkdir /mnt/efi
mount /dev/sda1 /mnt/efi
```

## PACSTRAP

Now its time to enjoy the moment that we will presence, because we will pacstrap. This will be the first time we will attempt to install the operating system, in other words, this will the base of uor SO when we will finish the installation. So then, we run the following command:
```Bash
pacstrap /mnt base linux linux-firmware dhcpcd
```
What we just done is install the kernel, the base OS and not less important, the dhcp client daemon. The last one its a "tool" that will meke easier the network configuration, because we want Internet connection...Dont we?

## CONFIGURATION
We have to first generate the file system, becuase we want to our changes persist and dont start again with the ISO image from 0. We can do this with fstab:
```Bash
genfstab -U /mnt >> /mnt/etc/fstab
```

Now, we have to get into chroot. This is how we will actually change our interaction to the installed system rather than the booted one, because obviusly we want to make changes in the enviroment that we just created and this is the better way to do it:
```Bash
arch-chroot /mnt
```

Now we have to sync to the hardware clock to our local time. (for me this is Europe/Madrid, but you can select yours from the appropriate folder /usr/share/zoneinfo/).
```Bash
ln -sf /usr/share/zoneinfo/Europe/Madrid /etc/localtime
ln -sf /usr/share/zoneinfo/GB /etc/localtime (FOR GB)
hwclock --systohc
```

Let’s set the system locale – first we will need an editor (I choose VIM but you can use nano)
```Bash
pacman -S vim
vim /etc/locale.gen  #We start writing with a, and we save & exit with esc+:+q+w+enter 
```
I simply un-commented the line I needed (es_ES.UTF-8 UTF-8). Then you run:
```Bash
locale-gen
```

Now let’s set the language with: (LANG=en_UK.UTF-8 if you selected english. Otherwise, US is default)
```Bash
echo "LANG=es_ES.UTF-8" > /etc/locale.conf
```

Let’s now set the default keyboard layout: (KEYMAP=uk or if you selected english)
```Bash
echo "KEYMAP=es" > /etc/vconsole.conf
```

Let’s do the hostname next – put in the Ipv4 localhost address and Ipv6 localhost address, and also the hostname (you can fully qualify this if you need a search suffix, or are in an environment with a domain etc.)
```Bash
echo "YourHostName" > /etc/hostname
```
Now we have to run the following commnand and we write this:
```Bash
vim /etc/hosts

127.0.0.1       localhost
::1             localhost
127.0.1.1       YourHostName
```

## ACCOUNT CONFIGURATION

Let’s set the root password, create a user and its password next: 
```Bash
passwd
useradd -m username # make a user named username (change the word username of course)
passwd username # set that user's password
usermod -aG wheel,audio,video,optical,storage username
```

Now we give the user we just created the necessary permisions for sudo:
```Bash
pacman -S sudo
EDITOR=vim visudo
```

We uncomment in that file the next line:
<pre>
%wheel ALL=(ALL) ALL
</pre>

## INSTALL THE BOOTLOADER (GRUB)

Now, we have to install the bootloader. There are a lo of these available, and there is a nice comparison chart here to allow you to make the best choice, that Arch Linux have made in its Wiki: https://wiki.archlinux.org/index.php/Arch_boot_process

Almost always I install GRUB, because its the one that i better know and its pretty easy to configure form me. But there is a little problem here, and its that the instalation for UEFI its different for BIOS. As you have seen in this installation, we are installing Arch in with a UEFI system, so if you are intereste in the BIOS way, take a look to this page: https://wiki.archlinux.org/index.php/GRUB#Installation_2

So then, we run this command:
```Bash
pacman -S grub efibootmgr
```

Next we need to install grub based on our efi mount point location for our UEFI system (/efi)
```Bash
grub-install --target=x86_64-efi --efi-directory=/efi --bootloader-id=GRUB
```

Now let’s generate the config file for grub:
```Bash
grub-mkconfig -o /boot/grub/grub.cfg
```

Finally, lets install netwokManager (To make easier the Internet conection) and lets reboot the system:
```Bash
pacman -S networkmanager
systemctl enable NetworkManager
exit
umount –l /mnt
sudo reboot now
```
#### IF YOU ARE INSTALLING ARCH IN A NORMAL COMPUTER, REMOVE ALL INSTALL MEDIA (USB OR OTHER DEVICES). ELSE, IF YOU ARE INSTALLING IT IN A VIRTUALBOX, SHUTDOWN AND REMOVE THE .ISO IMAGE.

At this poit, if GRUB loaded ArchLinux, you installed everything successfully the system. Now you should see a black screen with a command line, like the installation one. 

Now you can do the best part of Arch Linux: Select your desktop enviroment and your display manager if you want. If you are interested on this series and my installation guides, you can also see my <a href="https://github.com/Filipondios/Arch-Linux/blob/main/Installation/Desktop%20Enviroment%20Installation.md">Desktop enviroment installation guide</a>. Thats all!
