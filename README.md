# Gentoo installation

### Disk

- /dev/sda1 grub none 2M
- /dev/sda2 boot ext2 128M
- /dev/sda3 swap swap 512M
- /dev/sda4 root ext4 rest

```sh
mkswap /dev/sda3
swapon /dev/sda3
mount --types proc /proc /mnt/gentoo/proc
mount --rbind /sys /mnt/gentoo/sys
mount --make-rslave /mnt/gentoo/sys
mount --rbind /dev /mnt/gentoo/dev
mount --make-rslave /mnt/gentoo/dev 
mount /dev/sda2 /boot
```

### Date

```sh
ntpd -q -g
```

### Stage3 tarbar

```sh
tar xpvf stage3-*.tar.bz2 --xattrs-include='*.*' --numeric-owner
```

### Compile options, sources, configs

```sh
etc/
`- portage/
   `- make.conf
   `- repos.conf/
      `- gentoo.conf
`- resolv.conf
`- timezone
`- locale.gen
`- fstab
`- hosts
`- shadow (passwd)
`- rc.conf?
`- inittab?
`- conf.d/
   `- hostname
   `- net
   `- keymaps
   `- hwclock
`- init.d/
   `- net.eth0
```

### Software

```sh
emerge --ask app-admin/sysklogd && rc-update add sysklogd default
emerge --ask sys-process/cronie && rc-update add cronie default
emerge --ask sys-apps/mlocate
rc-update add sshd default
emerge --ask net-misc/dhcpcd
# Wireless
emerge --ask net-wireless/iw net-wireless/wpa_supplicant
emerge --ask --verbose sys-boot/grub:2
```

### Commands

```sh
mirrorselect -i -o >> /mnt/gentoo/etc/portage/make.conf
cp /mnt/gentoo/usr/share/portage/config/repos.conf /mnt/gentoo/etc/portage/repos.conf/gentoo.conf
chroot /mnt/gentoo /bin/bash
. /etc/profile
emerge-webrsync
eselect profile list|set 1
emerge --ask --verbose --update --deep --newuse @world
emerge --config sys-libs/timezone-data # Updating /etc/localtime
locale-gen
eselect locale list|set 2
env-update && . /etc/profile
emerge --ask sys-kernel/gentoo-sources
cd /usr/src/linux && make menuconfig
make && make modules_install && make install
emerge --ask sys-kernel/linux-firmware
emerge --ask net-misc/netifrc
ln -s net.lo net.eth0 && rc-update add net.eth0 default
grub-install /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg
exit
umount -l /mnt/gentoo/dev{/shm,/pts,}
umount -R /mnt/gentoo
reboot
```

### Notes

| Commnd | Description |
| ------ | ----------- |
| emerge-webrsync | Install a snapshot of the main ebuild repository which contains a collection of files that informs Portage about available software titles (for installation), which profiles the system administrator can select, package or profile specific news items, etc. |
| emerge --sync [--quiet] | Updating ebuild repositiry |
| emerge --ask --verbose --update --deep --newuse @world | Updating the @world set |
| emerge --info | Current active USE settings; Descriptions: /usr/portage/profiles/use.desc; Change the default: /etc/portage/make.conf |
| emerge --config sys-libs/timezone-data | Updating timezone based on /etc/timezone data |

```sh
eselect news list|read|purge (man news.eselect)
eselect profile list|set 2
eselect locale list|set 2
```
### USE

savedconfig
https://wiki.gentoo.org/wiki/Radeon

glamor
https://wiki.gentoo.org/wiki/Radeon


### CONCEPTS

* portage - software management tool
* emerge - tool for work with portage
* repository - collections of ebuilds
* packages - software titles in Gentoo repository
* ebuilds - files with all information portage needs to maintain software (search, install) /usr/portage
