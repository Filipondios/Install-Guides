# Arch Linux Graphical Enviroment Installation

Now that Arch is installed in our device, we need a display manager / desktop enviroment. There are plenty of them, so we can choose one of them from the ArchWiki: https://wiki.archlinux.org/title/display_manager. You can also install <a href="https://wiki.archlinux.org/title/LightDM">Lightdm</a> or <a href="https://wiki.archlinux.org/title/LightDM">Sddm</a> on every desktop enviroment to get a loging display manager, so its your choice. (There are more login diplays out there, but those are the most popular). I recommend you sddm because its easier to install.

**ATTENTION: Before you install the graphical enviroment, be sure that you dont have a bad configuration in the keyboard layout. If you have this error, the solution is the next one (in the command line);**
```Bash
localectl status # Should appear X11 Layout: n/a
localectl list-locales # Search your language, then Ctrl+z for exit
localectl set-x11-keymap yourlayout
```

## LOGIN DISPLAY MANAGER

#### If you want <a href="https://wiki.archlinux.org/title/LightDM">Ligtdm</a> as login display:
```Bash
sudo pacman -S lightdm lightdm-gtk-greeter
```

Now open this file and uncomment greeter-session. Then write the second line:
```Bash
sudo vim /etc/lightdm/lightdm.conf
greeter-session=lightdm-gtk-greeter
```
Finally, run this command: 
```Bash
systemctl enable lightdm.service
```

#### If you want <a href="https://wiki.archlinux.org/title/SDDM">Sddm</a> as login display:
```Bash
sudo pacman –S sddm
sudo systemctl enable sddm
```
## WINDOW MANAGER
Instead of installing a desktop enviroment, that is full of bloat and consumes petabytes of RAM (lol), you can always install a window manager. They and are really light and they simply work with keybandings, so when you get used to these, they are extremelly fast. Before we start, we run this commands:
```Bash
sudo pacman -Syu
sudo pacman -S xorg xorg-server
```
First, i have to say that i3 its maybe better for begginers, and Dwm its harder and needs more experince from the user. So if you are a begginer, I highly recommend you i3, qtile or others. 

#### If you want to install <a href="https://wiki.archlinux.org/title/i3">i3</a>:
There are some i3 packages, i really recomend i3-gaps:
```bash
sudo pacman -S i3 #sudo pacman -S i3-gaps # sudo pacman -S i3-blocks
```
Install a terminal, for example:
```bash
sudo pacman -S xterm 
```

#### If you want to install <a href="https://wiki.archlinux.org/title/dwm">DWM</a>:
We have to download some packages:
```bash
cd ~/.config
git clone https://git.suckless.org/dwm
git clone https://git.suckless.org/st
git clone https://git.suckless.org/dmenu
```
Now go inside of dwm,st and dmenu folders and run this command:
```bash
sudo make clean install
```

## DESKTOP ENVIROMENT
First you have to update pacman and install xorg:
```Bash
sudo pacman -Syu
sudo pacman -S xorg xorg-server
```

#### If you want to install <a href="https://wiki.archlinux.org/title/MATE">MATE</a>:
```Bash
sudo pacman -S mate mate-extra
```

#### If you want to install <a href="https://wiki.archlinux.org/title/KDE">KDE</a>:
```Bash
sudo pacman -S plasma
```

#### If you want to install <a href="https://wiki.archlinux.org/title/GNOME">GNOME</a>:
```Bash
sudo pacman –S gnome
```

#### If you want to install <a href="https://wiki.archlinux.org/title/cinnamon">CINNAMON</a>:
```Bash
sudo pacman -S gnome-terminal cinnamon nemo-fileroller
```

#### If you want to install <a href="https://wiki.archlinux.org/title/xfce">XFCE</a>:
```Bash
sudo pacman –S xfce4 xfce4-goodies
```


