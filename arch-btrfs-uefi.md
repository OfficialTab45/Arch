loadkeys de

gdisk /dev/sda
gdisk /dev/sdb

mkfs.ext4 -L BOOT /dev/sda1
mkfs.vfat -F 32 -n EFI /dev/sda2
mkfs.btrfs -L ROOT /dev/sda3
mkswap -L SWAP /dev/sda4
mkfs.btrfs -L HOME /dev/sdb1

swapon /dev/sda4

#if you have a HDD mount it to /home

mount /dev/sda3 /mnt

btrfs sub create /mnt/@
btrfs sub create /mnt/@opt
btrfs sub create /mnt/@pkg
btrfs sub create /mnt/@snapshots
btrfs sub create /mnt/@log

umount /mnt

mount /dev/sdb1 /mnt
btrfs sub create /mnt/@home
btrfs sub create /mnt/@snapshots_home

umount /mnt

mount -o noatime,ssd,space_cache=v2,compress=lzo,subvol=@ /dev/sda3 /mnt
mkdir -p /mnt/{boot,home,opt,var/log,var/cache/pacman/pkg,btrfs,pool}

mount /dev/sda1 /mnt/boot
mkdir /mnt/boot/efi
mount /dev/sda2 /mnt/boot/efi

mount -o noatime,ssd,space_cache=v2,compress=lzo,subvol=@opt /dev/sda3 /mnt/opt
mount -o noatime,ssd,space_cache=v2,compress=lzo,subvol=@log /dev/sda3 /mnt/var/log
mount -o noatime,ssd,space_cache=v2,compress=lzo,subvol=@pkg /dev/sda3 /mnt/var/cache/pacman/pkg
mount -o noatime,space_cache=v2,compress=lzo,subvol=@home /dev/sdb1 /mnt/home
mount -o noatime,ssd,space_cache=v2,compress=lzo,subvolid=5 /dev/sda3 /mnt/btrfs
mount -o noatime,space_cache=v2,compress=lzo,subvolid=5 /dev/sdb1 /mnt/pool

pacstrap /mnt base base-devel grub btrfs-progs bash-completion efibootmgr dosfstools wpa_supplicant
genfstab -Lp /mnt >> /mnt/etc/fstab

arch-chroot /mnt

echo hostname > /etc/hostname
echo LANG=de_DE.UTF-8 > /etc/locale.conf
echo LANGUAGE=de_DE >> /etc/locale.conf
echo KEYMAP=de-latin1 > /etc/vconsole.conf
ln -sf /usr/share/zoneinfo/Europe/Berlin /etc/localtime

useradd -m -g users -G wheel,audio,video -s /bin/bash username
passwd root
passwd username
EDITOR=nano visudo (remove # from #wheel)

nano /etc/locale.gen

#remove # from de_DE.UTF-8

locale-gen

grub-mkconfig -o /boot/grub/grub.cfg

grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=grub
exit
umount -R /mnt

reboot

ip a

sudo dhcpcd networkcard

sudo pacman -S acpid ntp cronie avahi

sudo systemctl enable acpid.service avahi-daemon cronie ntpd

sudo ntpd -gq

sudo pacman -S xorg xorg-server xorg-xinit

sudo pacman -S lightdm lightdm-gtk-greeter

sudo pacman -S xfce4 xfce4-goodies

sudo systemctl enable lightdm.service


sudo pacman -S networkmanager network-manager-applet nm-connection-editor
sudo pacman -S alsa-tools alsa-utils pulseaudio-alsa pavucontrol
sudo systemctl enable NetworkManager

sudo pacman -S firefox-i18n-de firefox qt4 vlc
