## How to install Arch Linux
(These instructions were written by me, following the Arch Wiki, to install Arch in a VM &/or real machine)

(These instructions are addressed to people that want to try out arch, but don't know how to install it)

(You can use these instructions also on a real machine, the commands are the same)


NOTE: if you don't like using `vim`, please replace `vim` with `nano` or an editor of your choice

I suggest also checking out [the arch wiki](https://wiki.archlinux.org/index.php/Installation_Guide)

If you haven't already, download [the Arch ISO](https://www.archlinux.org/download).

### If you use a VM:
+ Open [VirtualBox](https://www.virtualbox.org/wiki/Downloads)

+ Click "New"

+ Click "Expert Mode"
	- Name: Arch
	- RAM: At least 1024 MB
	- Hard disk: Create a virtual hard disk now
	
+ Click "Create"
	- File Size: At least 20 GB
	- Hard disk file type: VDI
	- Storage on physical HDD: Dynamically allocated
	
+ Click "Create"

+ Select the new VM

+ Click "Settings"

+ Go into the "Storage" tab
	- Click on the CD/DVD icon
	- On the right, select the location of the Arch iso
	- Select "Live CD/DVD"
+ Go into the "Network " tab
	- Click on "NAT"
	- Select "Bridged adapter"
	- Choose the network interface you want to use for the Virtual Machine (for example wlp4s0 and enp3s0 are two network interfaces)
+ Click "Ok"
+ Start the VM

### If you use a real machine
+ Get a hold of a DVD/USB with at least 1GB of storage
	- USB: Using a tool like [Etcher](https://balena.io), flash the ISO onto the USB
	- DVD: using a tool like [Brasero](https://wiki.gnome.org/Apps/Brasero/Documentation#Installing) on Linux or [ImgBurn](https://imgburn.com/index.php?act=download) on Windows, flash the ISO onto the DVD.
+ Put the DVD/USB in your machine
+ Open the BIOS/UEFI and [modify the boot order](https://www.pcsteps.com/1508-change-the-boot-order-usb-dvd-bios-uefi/), choosing the DVD/USB as the first boot device.

## SWAP partition
Please note: follow [these instructions](https://www.linux.com/news/all-about-linux-swap-space/) on how to allocate SWAP.

For example, if you gave 1GB of ram to the VM, you should create a SWAP partition of 2GB (twice the size of RAM).

Generally, modern machines have at least 4GB of ram, meaning that you should create a swap partition of at least half the size of ram (servers), but it's recommended to have a SWAP partition two times larger than the available RAM (desktops) (as written in the article linked above)

***

## After booting into the VM:

+ Choose "Arch Linux", then hit [ENTER]

(First of all I ran `loadkeys it` because my keyboard has an italian keyboard layout, but this is not needed, if you have a keyboard with an american keyboard layout)

## [Set the keyboard layout](https://wiki.archlinux.org/index.php/Installation_Guide#Set_the_keyboard_layout)

To find out available keyboard layouts, run: `ls /usr/share/kbd/keymaps/**/*.map.gz`

Once you find your keyboard layout, run: `loadkeys <keyboard-layout>`

## 1) [Connect to the internet](https://wiki.archlinux.org/index.php/Installation_Guide#Connect_to_the_internet)
Note: if you want to use Wi-Fi, run `wifi-menu` to connect to a wireless network.
If, for some reason, `wifi-menu` fails, run these two commands:

(If the wireless network interface isn't `wlan0`, use the one you have)

```
wpa_passphrase "My Network" "My passphrase" > wpa.conf
wpa_supplicant -i wlan0 -c wpa.conf
```

- 1.1) Check the network interfaces: `ip link`
- 1.2) If the above looks good, check if internet works: `ping archlinux.org`
+ 1.3) If you can't connect to `archlinux.org`, make sure you run: `dhcpcd <interface>`
  - 1.3.1) if you connect wirelessly, use `wlp4s0`, `wlan0` or any other wireless interface
  - 1.3.2) if you connect via ethernet, use `enp2s0`, `eth0`, or any other wired interface
- 1.4) If the above looks good, sync the clock: `timedatectl set-ntp true`

## 2) [Partition the disk](https://wiki.archlinux.org/index.php/Installation_Guide#Partition_the_disks)
(NOTE: we will be using a 20GB virtual HDD, if you use a real machine, you need to use bigger values. Please read the SWAP section above before continuing)

(Let's say you have a 500GB HDD and you want to create a SWAP partition of 4GB, you need to make an ext4 partition of 500-4 = 496GB, and a swap partition of 4GB, replacing "18G" with "496G" and "2G" with "4G".)

NOTE: If you already have partitions (from Window$ or another Operating System), we need to delete them and convert the partition table to MBR. Run `gdisk /dev/sda`, then `r`, then `g`, then `w`. After this, using one of the tools below, delete the partition table, and create a new one.

+ USING `fdisk` (like the terminal prompt, not user friendly): 
	- 2.1) `fdisk /dev/sda`
	- 2.2) Create a new partition (ext4): n, p, 1, 2048, +18G, [ENTER]
	- 2.3) Create a new partition (swap): n, p, 2, [ENTER], +2G, [ENTER]
	- 2.4) Change partition type: t, 2, 82
	- 2.5) Write changes to disk: w
	- 2.6) Quit

+ USING `cfdisk` (graphical, more user friendly):
	- 2.1) `cfdisk`
	- 2.2) Select "dos"
	- 2.2) Create a new partition (ext4): 18G, primary
	- 2.3) Make the partition above bootable
	- 2.4) Create a new partition (swap): 2G, primary
	- 2.5) Change the type of the partition above and select "82", which is swap
	- 2.6) Write changes to disk
	- 2.7) Quit

## 3) [Create the file system](https://wiki.archlinux.org/index.php/Installation_Guide#Format_the_partitions)
+ 3.1) ext4: `mkfs.ext4 /dev/sda1`
+ 3.2) swap: `mkswap /dev/sda2`

## 4) [Mount the partitions](https://wiki.archlinux.org/index.php/Installation_Guide#Mount_the_file_systems)
+ 4.1) Mount the disk space: `mount /dev/sda1 /mnt`
+ 4.2) Mount the swap: `swapon /dev/sda2`

## 5) [Installation: modifying the mirror list](https://wiki.archlinux.org/index.php/Installation_Guide#Installation)
(If you don't like using vim, use nano)

Update /etc/pacman.d/mirrorlist:
+ 5.1) `vim /etc/pacman.d/mirrorlist`
+ 5.2) Select a server closest to you, for example Italy, and delete all of the lines, keeping only the server you chose.
+ 5.3) Save and quit

## 6) [Install essential packages](https://wiki.archlinux.org/index.php/Installation_Guide#Install_essential_packages)
+ 6.1) `pacstrap /mnt base base-devel linux linux-firmware linux-headers man-db man-pages`

## 7) [Configuring the system: Fstab](https://wiki.archlinux.org/index.php/Installation_Guide#Fstab)

+ 7.1) Create fstab: `genfstab -U /mnt >> /mnt/etc/fstab`

## 8) [Configuring the system: Chroot](https://wiki.archlinux.org/index.php/Installation_Guide#Chroot)
+ 8.1) Change root into the new system: `arch-chroot /mnt`
+ 8.2) Install vim to modify files: `pacman -S vim`
(Change `vim` to `nano` if you don't like vim)

## 9) [Localization](https://wiki.archlinux.org/index.php/Installation_Guide#Localization)
(I will be using the Italian time; to select other timezones, press [TAB] after /zoneinfo/)

+ 9.1) Timezone: `ln -sf /usr/share/zoneinfo/Europe/Rome /etc/localtime`
+ 9.2) Sync the clock: `hwclock --systohc`

+ 9.3) Set the language(s):
	- 9.3.1) `vim /etc/locale.gen`
	- 9.3.2) Uncomment `en_US.UTF-8 UTF-8`, then save and quit
	- 9.3.3) `vim /etc/locale.conf`
	- 9.3.4) Add `LANG=en_US.UTF-8`, then save and quit
	- 9.3.5) Generate the locale: `locale-gen`
+ 9.4) Set the keyboard layout:
	- 9.4.1) `vim /etc/vconsole.conf`
	- 9.4.2) Add "KEYMAP=it", then save and quit
(Here after `KEYMAP` you need to type your keyboard's layout, you chose this before when you ran `loadkeys <code>`)
(I chose `it` because it's the Italian keyboard layout I chose at the beginning; as I said it's unnecessary if you have a keyboard with an american keyboard layout)

## 10) [Network Configuration: hostname](https://wiki.archlinux.org/index.php/Installation_Guide#Network_configuration)
+ 10.1) vim /etc/hostname
+ 10.2) Write an hostname, like "arch" 

## 11) [Network Configuration: hosts](https://wiki.archlinux.org/index.php/Installation_Guide#Network_configuration)
+ 11.1) `vim /etc/hosts`
+ 11.2) Add the following lines:

```
# IPv4
127.0.0.1	localhost
127.0.1.1	<your hostname>.localdomain <your hostname>

# IPv6
::1		localhost
```


+ 11.3) Save the file and exit

## 12) [Set the root password](https://wiki.archlinux.org/index.php/Installation_Guide#Root_password)
+ 12.1) `passwd`

## 13 Install sudo: 
+ 13.1) `pacman -S sudo`

## 14) Add a new user:
(for example I chose `matteo` so I will replace `<user>` with `matteo`)
+ 14.1) `useradd -m <user>`
+ 14.2) Add a password to the user: `passwd <user>`
+ 14.3) Add the user to necessary groups: `usermod -aG wheel,audio,video,optical,storage <user>`
+ 14.4) Check user is part of the groups we added above: `groups <user>`
+ 14.5) Add user to sudoers: `visudo`
	- 14.6) Search `# %wheel ALL=(ALL) ALL`, remove the `#` at the beginning

## 15) [Install the bootloader](https://wiki.archlinux.org/index.php/Installation_Guide#Boot_loader)
+ 15.1) `pacman -S grub efibootmgr os-prober freetype2 dosfstools`
+ 15.2) Install grub to disk: `grub-install /dev/sda`
+ 15.3) Configure grub: `grub-mkconfig -o /boot/grub/grub.cfg`

## 16) Install the network tools
+ 16.1) `pacman -S dhcpcd net-tools`
+ 16.2) After the above is done, enable `dhcpcd`: `systemctl enable dhcpcd`

## 17) [Reboot](https://wiki.archlinux.org/index.php/Installation_Guide#Reboot)
+ 17.1) Exit out of chroot: `exit`
+ 17.2) Poweroff the machine: `poweroff`
+ 17.3) Go into the VM settings and remove the Arch ISO
+ 17.4) Start the VM

(If you do this on a real machine make sure to remove the Arch Live CD/DVD/USB)

## 18) [Post-installation](https://wiki.archlinux.org/index.php/Installation_Guide#Post-installation)
+ 18.1) To login, type the `<user>`'s name
+ 18.2) Type the password you chose for `<user>` (it will be invisible)

## 19) [Install X.Org](https://wiki.archlinux.org/index.php/Xorg#Installation)
+ 19.1) Install X.Org: `sudo pacman -S xorg xorg-xinit`
+ 19.2) Select all packages by pressing [ENTER]

## 20) [Install a Desktop Environment](https://wiki.archlinux.org/index.php/Desktop_environment#List_of_desktop_environments)
NOTE: Instead of using a Desktop Environment, you can directly install a Window Manager. I use [i3](https://wiki.archlinux.org/index.php/i3). If you would like to use i3, follow this guide I wrote [here](https://godzm4tt3o.js.org/dotfiles/).

Please choose one of the following Desktop Environments:

- [Budgie](https://wiki.archlinux.org/index.php/Budgie): `sudo pacman -S gnome budgie-desktop`
- Cinnamon ([please read](https://wiki.archlinux.org/index.php/Cinnamon#Installation)): `sudo pacman -S cinnamon lightdm`
- Deepin ([please read](https://wiki.archlinux.org/index.php/Deepin#Installation)): `sudo pacman -S deepin deepin-extra networkmanager lightdm`
- [GNOME](https://wiki.archlinux.org/index.php/GNOME): `sudo pacman -S gnome gnome-extra networkmanager`
- [KDE Plasma](https://wiki.archlinux.org/index.php/KDE_Plasma): `sudo pacman -S plasma kde-applications sddm`
- [LXDE](https://wiki.archlinux.org/index.php/LXDE): `sudo pacman -S lxde lxdm`
- [LXQt](https://wiki.archlinux.org/index.php/LXQt): `sudo pacman -S lxqt breeze-icons sddm`
- [MATE](https://wiki.archlinux.org/index.php/MATE): `sudo pacman -S mate mate-extra lightdm`
- [XFCE](https://wiki.archlinux.org/index.php/Xfce): `sudo pacman -S xfce4 xfce4-goodies lightdm`

After installing Xorg, a Desktop Environment and its dependencies, create a new file called .xinitrc.
I have provided examples for all of the DEs above.
+ 20.1) `vim ~/.xinitrc`
+ 20.2) For example, if you chose GNOME, paste the code into the file above.
  - Budgie:
       ```
       export XDG_CURRENT_DESKTOP=Budgie:GNOME
       exec budgie-desktop
       ```
       
  - Cinnamon:
     
      `exec cinnamon-session`
     
  - Deepin:
  
     `exec startdde`

  - GNOME:
  
       ```
       export XDG_SESSION_TYPE=x11
       
       export GDK_BACKEND=x11
       
       exec gnome-session
       ```
       
  - KDE Plasma:
  
       `exec startplasma-x11`
       
  - LXDE:
       
       `exec startlxde`
       
  - LXQt: 
       
       `exec startlxqt`
       
  - MATE:
       
       `exec mate-session`
       
  - XFCE4:
       
       `exec startxfce4`

+ 20.3) After writing the correct text, save the file and exit.
+ 20.4) Now, to get into the Desktop Environment, all you have to run is `startx`.

+ 20.5) Now we also need to enable the Display Manager.
(If a Display Manager does not load, please refer to [this page](https://wiki.archlinux.org/index.php/Display_manager#Loading_the_display_manager)

  - GDM (Budgie, GNOME):
  
  `sudo systemctl enable gdm`

  - LightDM (Cinnamon, Deepin, MATE, XFCE):
  
  `sudo systemctl enable lightdm`
  
  
  - LXDM (LXDE):
  
  `sudo systemctl enable lxdm`
  
  - SDDM (KDE Plasma, LXQt):
  
  `sudo systemctl enable sddm`


## 21) [Install necessary drivers](https://wiki.archlinux.org/index.php/Xorg#Driver_installation)
+ AMD: `sudo pacman -S mesa xf86-input-libinput xf86-video-ati xf86-video-amdgpu vulkan-radeon amd-ucode`
+ Intel: `sudo pacman -S mesa xf86-input-libinput xf86-video-intel vulkan-intel intel-ucode`
+ NVIDIA: `sudo pacman -S mesa xf86-input-libinput nvidia xf86-video-nouveau nvidia-utils`

(NOTE: other video drivers can be found in the `xorg-drivers` group)

## 22) [Install audio sound system](https://wiki.archlinux.org/index.php/Sound_system)
(ALSA is already present in the stock kernel)

`sudo pacman -S pulseaudio pulseaudio-alsa alsa-utils`

You might also want to install some codecs (audio, image & video):

`sudo pacman -S flac faac wavpack libmad opus libvorbis openjpeg libwebp x265 libde265 x264 libmpeg2 libvpx`

## 23) [Enable touchpad tap to click](https://wiki.archlinux.org/index.php/Libinput)
Since we installed `xf86-input-libinput` in [Step 21](#21-install-necessary-drivers), we need to tweak the configuration.

+ 23.1) Copy the default configuration: `sudo cp /usr/share/X11/xorg.conf.d/40-libinput.conf /etc/X11/xorg.conf.d`
+ 23.2) Enable touchpad tap to click: `sudo vim /etc/X11/xorg.conf.d/40-libinput.conf`
	- 23.2.1) Find `Identifier "libinput touchpad catchall"`
	- 23.2.2) Under `Driver "libinput"` add this line: `Option "Tapping" "on"`
	- 23.2.3) Save and quit
	The touchpad config should look like this:
	```
	Section "InputClass"
		Identifier "libinput touchpad catchall"
		MatchIsTouchpad "on"
		MatchDevicePath "/dev/input/event*"
		Driver "libinput"
		Option "Tapping" "on"
	EndSection
	```

## 24) Pacman easter egg
+ 24.1) Modify `/etc/pacman.conf`: `sudo vim /etc/pacman.conf`
+ 24.2) Find the line that says `# Misc options`
+ 24.3) Remove the `#` before `Color`
+ 24.4) Below `#VerbosePkgLists` add this line: `ILoveCandy`

Each time you will install a new package you will see a little pacman in the progress bar.
The relevant `/etc/pacman.conf` should look like this:
```
# Misc options
#UseSysLog
Color
#TotalDownload
CheckSpace
#VerbosePkgLists
ILoveCandy
```

***

How to manage packages on Arch Linux:
+ To upgrade Arch Linux, run: `sudo pacman -Syu`
+ To install a package, run: `sudo pacman -S <package>`
+ To remove a package, run: `sudo pacman -Rns <package>`
+ To search a package in the official repositories, run: `pacman -Ss package-name`
+ To search a package you installed, run: `pacman -Qs package-name`
+ To clear the package cache, run: `sudo pacman -Sc`

***

Now you installed Arch Linux, enjoy!

You can improve these instructions by [creating a new pull request](https://github.com/GoDzM4TT3O/Arch/compare).

You can also create [a new issue](https://github.com/GoDzM4TT3O/Arch/issues/new/choose).
