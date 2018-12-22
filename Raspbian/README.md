# Raspbian

## Hardware
* 3x Raspberry Pi 3B+ [https://www.rasppishop.de/Raspberry-Pi-3-Model-B-14-GHz-64Bit-Quad-Core]
* 3x Samsung EVO Plus Micro SDHC 32GB [https://www.amazon.de/gp/product/B06XFSZGCC/ref=oh_aui_detailpage_o00_s02?ie=UTF8&psc=1]
* Stackable Case [https://www.amazon.de/gp/product/B079HN5YRZ/ref=oh_aui_detailpage_o09_s00?ie=UTF8&psc=1]
* TP-Link TL-SG105 5-Port Gigabit [https://www.amazon.de/gp/product/B00A128S24/ref=oh_aui_detailpage_o00_s01?ie=UTF8&psc=1]
* Anker PowerPort 6 PowerSupply [https://www.amazon.de/gp/product/B00PTLSH9G/ref=oh_aui_detailpage_o00_s00?ie=UTF8&psc=1]
* Anker Powerline Mirco USB 0.3m [https://www.amazon.de/gp/product/B016BEVNK4/ref=oh_aui_detailpage_o00_s01?ie=UTF8&psc=1]

## Setup

| Hostname | IP | Function |
| --- | --- | --- |
| kubepi01 | 192.168.178.10 | Master |
| kubepi02 | 192.168.178.11 | Node 1 |
| kubepi03 | 192.168.178.12 | Node 2 |

Write Raspian Lite image to SD cards.

Place emtpy file named `ssh` on SD boot partition to enable SSH on boot.

Identify the raspberry's IP addresses from router, configure static IPs, disable wlan and set hostname.

`/etc/dhcpcd.conf`

```
interface eth0
static ip_address=192.168.178.10/24
static routers=192.168.178.1
static domain_name_servers=192.168.178.1
```

Login as user `pi` and change the default password with `passwd`.

# TODO
* Static IP bisher nur auf .10
* weitere auf Static
* Wlan deaktivieren
* Hostnamen setzen
* Passwort ändern
