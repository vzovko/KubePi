# Raspbian

## Hardware
* [3x Raspberry Pi 3B+](https://www.rasppishop.de/Raspberry-Pi-3-Model-B-14-GHz-64Bit-Quad-Core)
* [3x Samsung EVO Plus Micro SDHC 32GB](https://www.amazon.de/gp/product/B06XFSZGCC/ref=oh_aui_detailpage_o00_s02?ie=UTF8&psc=1)
* [Stackable Case](https://www.amazon.de/gp/product/B079HN5YRZ/ref=oh_aui_detailpage_o09_s00?ie=UTF8&psc=1)
* [TP-Link TL-SG105 5-Port Gigabit](https://www.amazon.de/gp/product/B00A128S24/ref=oh_aui_detailpage_o00_s01?ie=UTF8&psc=1)
* [Anker PowerPort 6 PowerSupply](https://www.amazon.de/gp/product/B00PTLSH9G/ref=oh_aui_detailpage_o00_s00?ie=UTF8&psc=1)
* [Anker Powerline Mirco USB 0.3m](https://www.amazon.de/gp/product/B016BEVNK4/ref=oh_aui_detailpage_o00_s01?ie=UTF8&psc=1)

## Setup
| Hostname | IP | Function |
| --- | --- | --- |
| kubepi01 | 192.168.178.10 | Master |
| kubepi02 | 192.168.178.11 | Node 1 |
| kubepi03 | 192.168.178.12 | Node 2 |

Shared storage will be a NFS share on a Synology NAS.

Write Raspian Lite image to SD cards. [Refer to the Raspberry Pi documentation](https://www.raspberrypi.org/documentation/installation/installing-images/README.md).

Place an emtpy file named `ssh` on SD boot partition to enable SSH on boot.

Start the Raspberrys and identify their IP addresses from router and log in with user `pi` and default password `raspberry`.
```
ssh pi@192.168.178.x
```

### Inital configuration with raspi-config
Become root with `sudo -i` and start initial configuration with `raspi-config`.

Modify password via  `Change user password`.

Set hostname via `Network Options > Hostname > kubepi01`.

Configure static IP for eth0 in `/etc/dhcpcd.conf`.

```
interface eth0
static ip_address=192.168.178.10/24
static routers=192.168.178.1
static domain_name_servers=192.168.178.1
```

Disable wlan0 and bluebooth in `/etc/boot.cfg`.

```
dtoverlay=pi3-disable-wifi
dtoverlay=pi3-disable-bt
```

`reboot` to finish the intial setup.

Repeat the above for every Raspberry, modifiying the hostname and IP address.

## Continue with
[Ansible](https://github.com/vzovko/KubePi/tree/master/Ansible)