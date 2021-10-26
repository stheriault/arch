# assumptions
- uses wifi
  - wlan0
  - private_5g
- uses EFI bootloader

# setting up network

`iwctl`

```
[iwd]# device list
[iwd]# station wlan0 scan
[iwd]# station wlan0 get-networks
[iwd]# station wlan0 connect private_5g
```
