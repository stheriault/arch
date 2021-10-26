# assumptions
- uses EFI bootloader
- uses wifi
  - wlan0
  - private_5g
- /dev/sda5 is swap
- /dev/sda6 is root partition

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
```
# install
`pacstrap /mnt base linux linux-firmware vim wpa_supplicant wireless_tools networkmanager`

# configure
```
genfstab -L /mnt >> /mnt/etc/fstab
arch-chroot /mnt
```
