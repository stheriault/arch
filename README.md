# assumptions
- uses EFI bootloader
- uses wifi
  - wlan0
  - private_5g
- /dev/sda5 is swap
- /dev/sda6 is root partition
- pacific timezone

# setting up network

`iwctl`

```
[iwd]# device list
[iwd]# station wlan0 scan
[iwd]# station wlan0 get-networks
[iwd]# station wlan0 connect private_5g
```

`ping google.com`

# clock
`timedatectl set-ntp true`

# partitions
```
fdisk -l
```
_partition disks_

# format
```
mkft.ext4 /dev/sda6
mkswap /dev/sda5
```

# mount
```
mount /dev/sda6 /mnt
swapon /dev/sda5
mkdir /boot/efi
mount /dev/sda1 /mnt/boot/efi
```

# install
`pacstrap /mnt base linux linux-firmware vim wpa_supplicant wireless_tools networkmanager intel-ucode`

# configure
```
genfstab -L /mnt >> /mnt/etc/fstab
arch-chroot /mnt
```

# timezone
```
ln -sf /usr/share/zoneinfo/America/Los_Angeles /etc/localtime
hwclock --systohc
```

# localization
edit `/etc/locale.gen` and uncomment `en_US.UTF-8 UTF-8`
```
locale-gen
echo "LANG=en_US.UTF-8" > /etc/locale.conf
```

# networking
```
echo "golf" > /etc/hostname
echo "127.0.0.1 localhost" >  /etc/hosts
echo "::1       localhost" >> /etc/hosts
echo "127.0.0.1 golf"      >> /etc/hosts
```

# set password
`passwd`

# bootloader
```
pacman -S grub efibootmgr os-prober
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB
echo "GRUB_DISABLE_OS_PROBER=false" >> /etc/default/grub
```
modify the timeout to 1 second

```
vim /boot/grub/grub.cfg
grub-mkconfig -o /boot/grub/grub.cfg
```

# reboot
```
exit
reboot
```

# networking
```
systemctl enable NetworkManager.service
systemctl disable dhcpcd.service
systemctl enable wpa_supplicant.service
systemctl start NetworkManager.service
nmcli device wifi list
nmcli device wifi connect private_5g password #####
```

# user
```
pacman -S sudo
useradd -m -G wheel,storage,power steven
passwd steven
EDITOR=vim visudo
```
uncomment %wheel ALL=(ALL) ALL

# xorg, awesome, sound, time
```
pacman -S xf86-video-intel xorg xorg-server awesome xorg-xinit pavucontrol pulseaudio pulseaudio-alsa
systemctl enable systemd-timesyncd.service
```

# auto login
```
systemctl edit getty\@tty1.service
```
add this to file
```
[Service]
ExecStart=
ExecStart=-/usr/bin/agetty -a steven --noclear %I $TERM
```

# install my core utils
pacman -S alacritty zsh stow chromium

# Set up github ssh key
```
ssh-keygen -t ed25519 -C "stheriault@gmail.com"
cat .ssh/id_ed25519.pub
```
go to github.com/stheriault and add key

# clone my dotfiles
```
git clone git@github.com:stheriault/.dotfiles.git
cd .dotfiles
./install
```

