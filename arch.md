CUM INSTALĂM ARCH LINUX

# VERIFICĂ TASTATURA
loadkeys us

# VERIFICĂ REȚEAUA
dhcpcd

# TESTEAZĂ REȚEAUA
ping -c 3 www.google.com

# VERIFICĂ PLACA DE REȚEA
ip link

# ACTUALIZARE DATĂ ȘI ORĂ
timedatectl set-ntp true
timedatectl set-timezone Europe/Bucharest
heclock --systohc --utc

# VERIFICĂ DATA ȘI ORA
date

# SINCRONIZARE SYSTEM
pacman -Syy

# INSTALARE "reflector"
pacman -S reflector

# GENERARE "mirrorlist"
reflector -c "Romania" -f 12 -l 10 -n 12 --save /etc/pacman.d/mirrorlist

# CREARE PARTIȚIE (MARCHEAZĂ CA ȘI BOOTABILĂ)
fdisk -l
cfdisk /dev/sda
mkfs.ext4 /dev/sda1

# MONTARE PARTIȚIE
mount /dev/sda1 /mnt

# INSTALARE "base" ȘI "base-devel"
pacstrap -i /mnt base base-devel

# DACĂ SUNT ERORI, EXECUTĂ
pacman-key --init
pacman-key --populate archlinux
pacman-key --refresh-keys

# GENERARE "fstab"
genfstab -U -p /mnt >> /mnt/etc/fstab

# ACCESARE "chroot"
arch-chroot /mnt /bin/bash

# INSTALARE "nano"
pacman -S nano

# SELECTARE "locale" (en_US.UTF-8 UTF-8, ro_RO.UTF-8 UTF-8)
nano /etc/locale.gen

# GENERARE "locale"
locale-gen

# SELECTARE "timezone"
ln -sf /usr/share/zoneinfo/Europe/Bucharest /etc/localtime

# ACTUALIZARE "hwclock"
hwclock --systohc --utc

# SETARE "hostname"
echo void > /etc/hostname

# EDITARE "hosts"
nano /etc/hosts

127.0.0.1	localhost.localdomain	localhost
::1			localhost.localdomain	localhost
127.0.1.1	localhost.localdomain	void

# SETEAZĂ PAROLA PENTRU "root"
passwd

# INSTALARE "grub", "linux" ȘI ALTE APLICAȚII…
pacman -S grub sudo dhcpcd netctl wpa_supplicant dialog linux linux-headers linux-firmware

# INSTALARE "grub" PE HDD
grub-install /dev/sda

# CREARE CONFIGURAȚIE "grub"
grub-mkconfig -o /boot/grub/grub.cfg

# SETĂM REȚEAUA SĂ PORNEASCĂ ODATĂ CU SISTEMUL
systemctl enable dhcpcd

# IEȘIM DIN "chroot"
exit

# DEMONTĂM PARTIȚIA "/mnt"
umount -R /mnt

# REPORNIM SISTEMUL
reboot

# CREĂM UN NOU UTILIZATOR
useradd -m -g users -G wheel -s /bin/bash void

# CREĂM PAROLA NOULUI UTILIZATOR
passwd void

# EDITĂM DREPTURILE "sudo" PENTRU UTILIZATORUL "void"
nano /etc/sudoers (ȘTERGEM "#" DIN FAȚA GRUPULUI "wheel")

# IEȘIM DIN MEDIUL "root"
exit

# INTRĂM ÎN MEDIUL UTILIZATORULUI NORMAL, SINCRONIZĂM SISTEMUL ȘI EFECTUĂM ACTUALIZĂRILE

sudo pacman -Syyu

# INSTALĂM MEDIUL AUDIO
sudo pacman -S pulseaudio pulseaudio-alsa alsa-utils

# INSTALĂM MEDIUL VIDEO
sudo pacman -S xorg xorg-xinit

# INSTALĂM PLACA VIDEO
sudo pacman -S xf86-video-ati

# INSTALĂM MEDIUL "i3"
sudo pacman -S i3-wm dmenu i3status xfce4-terminal firefox

# SETĂRI "xinitrc"
echo "exec i3" > ~/.xinitrc

# DACĂ NU EXISTĂ SUNET, EXECUTĂ COMANDA
alsactl init
