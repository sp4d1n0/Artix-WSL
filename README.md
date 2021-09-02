# Artix Linux WSL2 - how-to produce you own images

> Artix Linux is a rolling-release distribution, based on Arch Linux.
> It uses OpenRC, runit or s6 as init because PID1 must be simple, secure and stable.

![](https://github.com/sp4d1n0/Artix-WSL/blob/main/pics/artix.jpg)

## Content

Instructions to prepare images for all supported init system were provided. They all are functional, and it's mainly a personal prefence which one to choose. 
Been WSL2 a non real booted system, they provide just a thin layer to deal with services and deamons...

A launcher is provided, which can be renamed and reused to create as many variants you want. 
The launcher, beside converting the rootfs to an ext4.vhdx usable to WSL2, provide configuration tool, such as setting the default user and some WSL variables. Check all its flags in a command line.

### For the impatients: an example minimal prebuilt image is provided (runit), inside ./rootfs. Just join the three chunk together - 7-Zip is able to do that - and put the resulting install.tar.gz in a new folder, with the given launcher. Enjoy! 
**Beware that's a very minimal setup, stripped to the very barebone!**

---

## HowTo

### RootFS
To prepare our barebone root filesystem, you'll use the bootstrap archive released by archlinux (artixlinux yet have no such an image to use)
- download the latest bootstrap from https://mirrors.edge.kernel.org/archlinux/iso/latest/
- tar -xzf archlinux-bootstrap-$YOURDOWNLOADEDVERSION-x86_64.tar.gz
- pushd root.x86_64
- tar -czpf ../install.tar.gz *
- popd
- copy your generated install.tar.gz to a folder
- copy the included launcher in the same folder
- run the launcher, which will prepare a new ext4.vhdx and register the new distro (*the name of the launcher will be the name of the registered distro, so rename it as you prefer...*)
- enter your new bare distro - through the launcher - and carefully follow the next steps:

### Migration (from https://wiki.artixlinux.org/Main/Migration)

As soon as you enter your new distro, proceed with those migration steps:
- curl https://raw.githubusercontent.com/sp4d1n0/Artix-WSL/main/etc/pacman.conf -o /etc/pacman.conf
- curl https://raw.githubusercontent.com/sp4d1n0/Artix-WSL/main/etc/pacman.d/mirrorlist -o /etc/pacman.d/mirrorlist
- pacman-key --init
- pacman-key --populate
- pacman -Sy
- pacman -S artix-keyring
- pacman-key --populate artix
- pacman-key --lsign-key 95AEC5D0C1E294FC9F82B253573A673A53C01BC2

#### Install

The migration guide is created from the perspective of a live system, so it list much more packages than the needed for a WSL2 image. If you want to follow strictly the migration guide, then you'll need the folloing packages:
- ```pacman -Sw base base-devel grub linux linux-headers netifrc mkinitcpio grub mkinitcpio archlinux-mirrorlist net-tools rsync nano lsb-release connman esysusers etmpfiles artix-branding-base``` 
However, if you want, you install just the very minimum:
- ```pacman -Sw base base-devel archlinux-mirrorlist lsb-release connman esysusers etmpfiles artix-branding-base```
And then, if needed, your preferred init system...

- - **OpenRC** 
```pacman -Sw openrc-system elogind-openrc openrc```
- - **runit** 
```pacman -Sw runit-system runit elogind-runit```
- - **s6** 
```pacman -Sw s6-system s6 elogind-s6```

#### Remove useless systemd
- pacman -Rdd --noconfirm systemd systemd-libs pacman-mirrorlist dbus

#### Install needed packages
- curl https://raw.githubusercontent.com/sp4d1n0/Artix-WSL/main/etc/pacman.d/mirrorlist -o /etc/pacman.d/mirrorlist
- pacman -Sy

- - ```pacman -Sy base base-devel grub linux linux-headers netifrc mkinitcpio grub mkinitcpio archlinux-mirrorlist net-tools rsync nano lsb-release connman esysusers etmpfiles artix-branding-base``` or
- - ```pacman -Sy base base-devel archlinux-mirrorlist lsb-release connman esysusers etmpfiles artix-branding-base```

and then one of the init system artix provide. Again, only if you want, you really do not need them in WSL2:
- - *openrc* 
```pacman -S openrc-systemopenrc elogind-openrc```
- - *runit* 
```pacman -S runit-system runit elogind-runit```
- - *s6*
```pacman -S s6-system s6 elogind-s6```
- - *66*
```pacman -S 66 elogind-66```

#### Reinstall all packages from Artix repositories
- mv /etc/artix-release /etc/artix-release-prev
- export LC_ALL=C
- pacman -Sl system | grep installed | cut -d" " -f2 | pacman -S -
- pacman -Sl world | grep installed | cut -d" " -f2 | pacman -S -
- pacman -Sl galaxy | grep installed | cut -d" " -f2 | pacman -S -

#### Remove junk
```
for user in journal journal-gateway timesync network bus-proxy journal-remote journal-upload resolve coredump; do
   userdel systemd-$user
 done
 rm -vfr /{etc,var/lib}/systemd
 ```
 
 #### Additional steps
 - pacman -S vim
 - vim /etc/pacman.con
 ```
 change 'SigLevel = Never' to 'SigLevel = Required DatabaseOptional'
 ```
 - rm /etc/artix-release-prev
 - pacman -Syuu
 - enjoy!
 
 #### Post Install
 - useradd -m $YOURNAME
 - usermod -G wheel $YOURNAME
 - visudo 
 - uncomment one of the lines which allow member of wheel group to issude commands with sudo
 - install whatever you want...
 
 ![](https://github.com/sp4d1n0/Artix-WSL/blob/main/pics/capture.png)

