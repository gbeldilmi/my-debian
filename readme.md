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

## Routing & Wireguard (server)

```sh
sudo nano /etc/sysctl.conf # net.ipv4.ip_forward=1
                           # net.ipv6.conf.all.forwarding=1
sudo sysctl -p

wg genkey | sudo tee /etc/wireguard/wg-private.key | wg pubkey | sudo tee /etc/wireguard/wg-public.key

sudo systemctl start wg-quick@wg.service
sudo systemctl enable wg-quick@wg.service

sudo ufw allow 51820/udp
sudo ufw allow OpenSSH
sudo ufw enable
sudo ufw status
```
