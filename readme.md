# debian-base

## For Raspberry Pi

Raspberry Pi OS Lite : use image customisation to configure hostname, ssh, user and locale.

### Partitions

Partition | Mount point | Size                   | Filesystem
--------- | ----------- | ---------------------- | ----------
/dev/sda1 | /boot       | 512Mo                  | EFI System
/dev/sda2 | /           | Remaining space        | ext4

## For x86_64

### Partitions

Partition            | Mount point | Size               | File system
-------------------- | ----------- | ------------------ | ----------------
/dev/sda1            |             | 512Mo              | EFI System
/dev/sda2            | /           | Remaining space    | ext4
/dev/sda3            |             | RAM size (max 8Go) | swap
/dev/sdb1 (optional) | /home       | The whole device   | ext4

### Software to install

- SSH server
- standard system utilities

### Post-installation

```sh
nano /etc/apt/sources.list # Comment out all lines beginning with "deb cdrom:..."
                           # Add contrib and non-free repositories

nano /etc/ssh/sshd_config # AllowUsers user
```

### Install missing firmware

```sh
apt install firmware-linux isenkram-cli
isenkram-autoinstall-firmware
```

## Wireless networks

```sh
apt install iwd
systemctl --now enable iwd
```

As normal user, run `iwctl`. 

## Install packages

Install all packages from the [package list](package-list.txt) with `apt install [packages]`.

Show system information when logging in.

```sh
echo "neofetch" >> /etc/profile
```

## Routing 

```sh
apt install isc-dhcp-server net-tools iptables iptables-persistent

vim /etc/sysctl.conf
sysctl -p

iptables -t nat -A POSTROUTING -o wlan0 -j MASQUERADE 
iptables6 -t nat -A POSTROUTING -o wlan0 -j MASQUERADE 
mkdir -p /etc/iptables
iptables-save > /etc/iptables/rules.v4
iptables6-save > /etc/iptables/rules.v6

mkdir /var/log/dhcp/
ln /etc/rsyslog.conf /etc/syslog.conf
echo "# Redirect dhcp service" >> /etc/syslog.conf
echo "local7.* /var/log/dhcp/dhcpd.log" >> /etc/syslog.conf
```

- [cf. `/etc/sysctl.conf`](etc/sysctl.conf)
- [cf. `/etc/network/interfaces`](etc/network/interfaces)
- [cf. `/etc/default/isc-dhcp-server`](etc/default/isc-dhcp-server)
- [cf. `/etc/dhcp/dhcpd.conf`](etc/dhcp/dhcpd.conf)


## Wireguard (server)

[cf. my-archlinux configuration](https://github.com/gbeldilmi/my-archlinux)

```sh
sudo vim /etc/sysctl.conf
sudo sysctl -p

wg genkey | sudo tee /etc/wireguard/wg0-private.key | wg pubkey | sudo tee /etc/wireguard/wg0-public.key
sudo vim /etc/wireguard/wg0.conf
sudo systemctl start wg-quick@wg0.service
sudo systemctl enable wg-quick@wg0.service

sudo ufw allow 51820/udp
sudo ufw allow OpenSSH
sudo ufw enable
sudo ufw status
```
