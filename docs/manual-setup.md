# Raspberry Pi 5 Setup

## Update all packages

```
apt update && apt full-upgrade -y
```

## Install latest eeprom version

```
rpi-eeprom-update

rpi-eeprom-update -d -a

reboot

rpi-eeprom-update

```

# Prepare nvme support

```
vi /boot/firmware/config.txt

...
dtparam=pciex1
#dtparam=pciex1_gen=3 # experimental Feb 2025
...

```

# Copy sd card to nvme

```
dd if=/dev/mmcblk0 of=/dev/nvme0n1 bs=4M status=progress
```

# Change boot order

```
raspi-config -> Advanced Options → Boot Order → NVMe First

reboot
```

# Remove sd card

shutdown -h now

# Change hostnamen and device ID

raspi-config -> Advanced Options ->

```
rm /etc/machine-id
systemd-machine-id-setup
```

# enable VLAN Setup

```
#nmcli con modify "Wired connection 1" ipv4.addresses 192.168.15.152/24 ipv4.gateway 192.168.15.1 ipv4.dns „192“.168.15.1 ipv4.method manual
#nmcli con down "Wired connection 1" && nmcli con up "Wired connection 1"


nmcli connection delete "Wired connection 1"
nmcli connection add type ethernet con-name "disabled-eth0" ifname eth0
nmcli connection modify "disabled-eth0" connection.autoconnect no

nmcli connection show
```
