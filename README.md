# Artix Linux WSL2 images

> Artix Linux is a rolling-release distribution, based on Arch Linux.
> It uses OpenRC, runit or s6 as init because PID1 must be simple, secure and stable.

## Content

Images for all supported init (OpenRC, RunIT and S6) are provided. They are built from the minimal archlinux bootstrap image.

---

### Used procedure (from https://wiki.artixlinux.org/Main/Migration)

- boot to a bare bootstrap rootfs
- curl https://raw.githubusercontent.com/sp4d1n0/Artix-WSL/main/etc/pacman.conf -o /etc/pacman.conf
- curl https://raw.githubusercontent.com/sp4d1n0/Artix-WSL/main/etc/pacman.d/mirrorlist -o /etc/pacman.d/mirrorlist
- pacman-key --init
- pacman-key --populate
- pacman -S artix-keyring
- pacman-key --populate artix
- pacman-key --lsign-key 95AEC5D0C1E294FC9F82B253573A673A53C01BC2

##### choose your init
- - **OpenRC** 
```pacman -Sw base base-devel openrc-system grub linux linux-headers elogind-openrc openrc netifrc grub mkinitcpio archlinux-mirrorlist net-tools rsync nano lsb-release esysusers etmpfiles```
- - **runit** 
```pacman -Sw base base-devel runit-system grub linux linux-headers runit elogind-runit grub mkinitcpio archlinux-mirrorlist net-tools rsync nano lsb-release esysusers etmpfiles```
- - **s6** 
```pacman -Sw base base-devel s6-system grub linux linux-headers s6 elogind-s6 grub mkinitcpio archlinux-mirrorlist net-tools rsync nano lsb-release esysusers etmpfiles```

##### remove useless systemd
- pacman -Rdd --noconfirm systemd systemd-libs pacman-mirrorlist dbus'

##### install needed packages
- curl https://raw.githubusercontent.com/sp4d1n0/Artix-WSL/main/etc/pacman.d/ mirrorlist -o /etc/pacman.d/mirrorlist
- - *openrc* 
```pacman -S base base-devel openrc-system grub linux linux-headers openrc elogind-openrc netifrc mkinitcpio grub mkinitcpio archlinux-mirrorlist net-tools rsync nano lsb-release connman esysusers etmpfiles artix-branding-base```
- - *runit* 
```pacman -S base base-devel runit-system grub linux linux-headers runit elogind-runit grub mkinitcpio archlinux-mirrorlist net-tools rsync nano lsb-release connman esysusers etmpfiles artix-branding-base```
- - *s6*
```pacman -S base base-devel s6-system grub linux linux-headers s6 elogind-s6 grub mkinitcpio archlinux-mirrorlist net-tools rsync nano lsb-release connman esysusers etmpfiles artix-branding-base```
- - *66*
```pacman -S base base-devel grub linux linux-headers 66 elogind-66 grub mkinitcpio archlinux-mirrorlist net-tools rsync nano lsb-release connman esysusers etmpfiles artix-branding-base```

### Reinstall all packages from Artix repositories
- mv /etc/artix-release /etc/artix-release-prev
- export LC_ALL=C
- pacman -Sl system | grep installed | cut -d" " -f2 | pacman -S -
- pacman -Sl world | grep installed | cut -d" " -f2 | pacman -S -
- pacman -Sl galaxy | grep installed | cut -d" " -f2 | pacman -S -

### Remove junky
-  
```
for user in journal journal-gateway timesync network bus-proxy journal-remote journal-upload resolve coredump; do
   userdel systemd-$user
 done
 rm -vfr /{etc,var/lib}/systemd
 ```
