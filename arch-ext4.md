loadkeys de

fdisk /dev/sda

mkfs.ext4 -L BOOT /dev/sda1
mkfs.ext4 -L ROOT /dev/sda2
mkswap -L SWAP /dev/sda3

mount /dev/sda2 /mnt
mkdir /mnt/boot
mount /dev/sda1 /mnt/boot
swapon /dev/sda3

nano /etc/pacman.d/mirrorlist

pacstrap /mnt base base-devel grub gvfs bash-completion

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

grub-mkconfig -o /boot/grub/grub.cfg
grub-install /dev/sda

exit
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
