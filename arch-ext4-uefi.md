UPDATED

loadkeys de

gdisk /dev/sdb

boot 300M
efi 100M

mkfs.ext4 -L BOOT /dev/sdb1
mkfs.vfat -F 32 -n EFI /dev/sdb2
mkfs.ext4 -L ROOT /dev/sdb3
mkswap -L SWAP /dev/sdb4

mount /dev/sdb3 /mnt
mkdir /mnt/boot
mkdir /mnt/home
mount /dev/sdb1 /mnt/boot
gdisk /dev/sda1 (hdd)
mount /dev/sda1 /mnt/home
swapon /dev/sda3

nano /etc/pacman.d/mirrorlist

pacstrap /mnt base base-devel grub gvfs bash-completion efibootmgr linux linux-firmware

genfstab -Lp /mnt >> /mnt/etc/genfstab

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

mkdir /boot/grub
grub-mkconfig -o /boot/grub/grub.cfg
grub-install --target=x86_64-efi --efi-directory=/boot/ --bootloader-id=grub

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
