# homelab

Docker stacks for homelab server, this stack contains:

- dnsdist (dns)
- recursor (dns)
- dnscollector (dns)
- wireguard (vpn)
- dufs (file sharing)
- jellyfin (videos)
- immich (photos)
- gonic (music)
- airsonic (music)
- traefik (proxy)

## Deploy

```bash
$ sudo docker network create traefik_proxy
$ cd network
$ sudo docker compose up -d

$ cd services
$ sudo docker compose up -d

$ cd medias/jellyfin
$ sudo docker compose up -d
```

## Storage

Datas are stored in `/mnt/data`.

Get disk id

```bash
$ sudo blkid
/dev/sda1: LABEL="Dock_Disk" BLOCK_SIZE="512" UUID="C4CADB0C" TYPE="ntfs" PARTLABEL="Basic data partition" PARTUUID="6fd39909-ba8e-4db7-b440-0c"
```

Create udev rules

```bash
$ cat /etc/udev/rules.d/99-custom-mount.rules
ACTION=="add", KERNEL=="sd[a-z][0-9]*", SUBSYSTEMS=="usb", SUBSYSTEM=="block", ENV{ID_FS_UUID}=="C4CADB0C", ENV{SYSTEMD_WANTS}="data-medias-dock_disk.mount"
```

Automount

```bash
$ cat /etc/systemd/system/data-medias-dock_disk.mount
[Unit]
Description=Mount /dev/disk/by-uuid/C4CADB0C at /mnt/data/disks/dock_disk

[Mount]
What=/dev/disk/by-uuid/C4CADB06CADAF420
Where=/mnt/data/disks/dock_disk
Type=auto
Options=noauto,nofail,uid=1000,gid=1000
```

Mount the disk

```bash
systemctl start data-medias-dock_disk.mount
```
