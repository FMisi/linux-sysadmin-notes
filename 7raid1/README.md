# RAID1 és LVM — RHEL / Rocky Linux jegyzet

A RAID (Redundant Array of Independent Disks) célja:

* redundancia
* adatbiztonság
* teljesítmény növelése
* hibatűrés

A RAID1 tükrözést (mirroring) használ:

* minimum 2 lemez kell
* ugyanaz az adat kerül mindkét lemezre
* ha az egyik lemez meghal, a rendszer tovább működik

A RAID1-hez ajánlott azonos méretű lemezeket használni.

---

# RAID szintek röviden

| RAID   | Mire jó       | Hibatűrés          |
| ------ | ------------- | ------------------ |
| RAID0  | sebesség      | nincs              |
| RAID1  | tükrözés      | 1 lemez kieshet    |
| RAID5  | parity        | 1 lemez kieshet    |
| RAID10 | RAID1 + RAID0 | gyors és redundáns |

---

# VirtualBox lab setup

A Rocky VM-hez adj:

* 2 extra virtuális lemezt
* pl:

  * 5 GB
  * 5 GB

VirtualBox:

* Settings
* Storage
* Add Hard Disk

---

# Lemezek ellenőrzése

```bash
lsblk
```

Példa:

```text
sda
sdb
sdc
```

Vagy részletesebben:

```bash
fdisk -l
```

---

# mdadm telepítése

Rocky / RHEL:

```bash
sudo dnf install mdadm -y
```

---

# Particionálás

Mindkét új lemezen hozz létre Linux RAID partíciót.

Példa:

```bash
sudo fdisk /dev/sdb
```

Lépések:

* n → új partíció
* p → primary
* Enter
* Enter
* Enter
* t → type
* fd → Linux RAID
* w → mentés

Ugyanez:

```bash
sudo fdisk /dev/sdc
```

---

# RAID1 tömb létrehozása

```bash
sudo mdadm --create --verbose /dev/md0 \
--level=1 \
--raid-devices=2 \
/dev/sdb1 /dev/sdc1
```

---

# RAID állapot ellenőrzése

```bash
cat /proc/mdstat
```

Részletes info:

```bash
sudo mdadm --detail /dev/md0
```

---

# Filesystem létrehozása

Ext4:

```bash
sudo mkfs.ext4 /dev/md0
```

XFS (RHEL-ben gyakori):

```bash
sudo mkfs.xfs /dev/md0
```

---

# Mount point létrehozása

```bash
sudo mkdir /data
```

Mount:

```bash
sudo mount /dev/md0 /data
```

---

# Automatikus mount — fstab

UUID lekérése:

```bash
blkid
```

fstab szerkesztése:

```bash
sudo nano /etc/fstab
```

Példa:

```fstab
UUID=valami-uuid /data xfs defaults 0 0
```

Teszt:

```bash
sudo mount -a
```

---

# RAID automatikus betöltése

Config generálás:

```bash
sudo mdadm --detail --scan | sudo tee -a /etc/mdadm.conf
```

initramfs frissítés:

```bash
sudo dracut -H -f /boot/initramfs-$(uname -r).img $(uname -r)
```

---

# RAID hiba szimuláció

Egy lemez hibásnak jelölése:

```bash
sudo mdadm /dev/md0 --fail /dev/sdb1
```

Eltávolítás:

```bash
sudo mdadm /dev/md0 --remove /dev/sdb1
```

Állapot:

```bash
cat /proc/mdstat
```

---

# Új lemez hozzáadása

```bash
sudo mdadm /dev/md0 --add /dev/sdb1
```

Újraépítés figyelése:

```bash
watch cat /proc/mdstat
```

---

# LVM alapok

Az LVM (Logical Volume Manager) lehetővé teszi:

* dinamikus storage kezelést
* online bővítést
* flexibilis partíciókezelést

Nagyon fontos enterprise skill.

---

# LVM fogalmak

| Fogalom | Jelentés        |
| ------- | --------------- |
| PV      | Physical Volume |
| VG      | Volume Group    |
| LV      | Logical Volume  |

---

# RAID + LVM kombináció

Nagyon gyakori enterprise setup:

```text
diszkek → RAID → LVM → filesystem
```

---

# Physical Volume létrehozása

```bash
sudo pvcreate /dev/md0
```

---

# Volume Group létrehozása

```bash
sudo vgcreate vgdata /dev/md0
```

---

# Logical Volume létrehozása

```bash
sudo lvcreate -L 2G -n lvdata vgdata
```

---

# Filesystem az LV-n

```bash
sudo mkfs.xfs /dev/vgdata/lvdata
```

---

# Mount

```bash
sudo mkdir /lvdata
```

```bash
sudo mount /dev/vgdata/lvdata /lvdata
```

---

# LVM bővítés

LV méretének növelése:

```bash
sudo lvextend -L +1G /dev/vgdata/lvdata
```

XFS filesystem növelése:

```bash
sudo xfs_growfs /lvdata
```

Ext4 esetén:

```bash
sudo resize2fs /dev/vgdata/lvdata
```

---

# Hasznos parancsok

## RAID

```bash
cat /proc/mdstat
mdadm --detail /dev/md0
```

---

## LVM

```bash
pvs
vgs
lvs
```

---

## Storage

```bash
lsblk
df -h
blkid
```

---

# Enterprise szempontból fontos

A RAID:

* nem backup
* csak redundancia

A backup továbbra is kötelező.

---

# Interjús fontos témák

* RAID1 vs RAID5
* mdadm
* storage troubleshooting
* degraded array
* filesystem resize
* LVM expand
* fstab
* UUID
* XFS vs ext4

---

# RHEL / Rocky specifikus fontos dolgok

* XFS alapértelmezett filesystem
* dracut
* mdadm
* LVM nagyon gyakori
* enterprise storage management alap skill

---

# Hasznos troubleshooting

RAID:

```bash
cat /proc/mdstat
```

Filesystem:

```bash
df -h
```

Mount problémák:

```bash
journalctl -xe
```

fstab teszt:

```bash
mount -a
```
