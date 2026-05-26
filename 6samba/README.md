# Samba

## 1. Telepítés

Rocky:

```bash
sudo dnf install samba samba-client -y
```

---

## 2. Share mappa

```bash
sudo mkdir -p /srv/samba/share
```

Jogok:

```bash
sudo chmod 2775 /srv/samba/share
```

Owner:

```bash
sudo chown -R sambauser:sambauser /srv/samba/share
```

---

## 3. Samba user

Linux user kell először:

```bash
sudo useradd sambauser
```

Linux jelszó:

```bash
sudo passwd sambauser
```

Samba jelszó:

```bash
sudo smbpasswd -a sambauser
```

---

## 4. Config

```bash
sudo nano /etc/samba/smb.conf
```

Aljára:

```conf
[share]
   path = /srv/samba/share
   browsable = yes
   writable = yes
   guest ok = no
   valid users = sambauser
   force user = sambauser
   create mask = 0664
   directory mask = 0775
```

Config ellenőrzés:

```bash
testparm
```

---

## 5. SELinux

SELinux context:

```bash
sudo chcon -Rt samba_share_t /srv/samba/share
```

Persistens context (a sima SELinux context nem kell itt eggyel feljebb, ha persistensre akarod):

```bash
sudo semanage fcontext -a -t samba_share_t "/srv/samba/share(/.*)?"
```

```bash
sudo restorecon -Rv /srv/samba/share
```

Ha nincs `semanage`:

```bash
sudo dnf install policycoreutils-python-utils -y
```

SELinux context ellenőrzés:

```bash
ls -Zd /srv/samba/share
```

Elvárt:

```text
samba_share_t
```

---

## 6. Firewall

```bash
sudo firewall-cmd --add-service=samba --permanent
```

```bash
sudo firewall-cmd --reload
```

---

## 7. Service indítás

```bash
sudo systemctl enable --now smb
```

```bash
sudo systemctl enable --now nmb
```

Status:

```bash
systemctl status smb
```

---

## 8. Debian kliens

Telepítés:

```bash
sudo apt install smbclient cifs-utils -y
```

Share lista:

```bash
smbclient -L //192.168.1.10 -U sambauser
```

---

## 9. Mount

```bash
sudo mkdir /mnt/share
```

Root mount:

```bash
sudo mount -t cifs //192.168.1.10/share /mnt/share \
-o username=sambauser,rw
```

Normál user mount:

```bash
sudo mount -t cifs //192.168.1.10/share /mnt/share \
-o username=sambauser,uid=$(id -u),gid=$(id -g),rw
```

---

## 10. Írás teszt

Debianon:

```bash
touch /mnt/share/teszt.txt
```

Rocky-n:

```bash
ls /srv/samba/share
```

Ha látod:

```text
teszt.txt
```

akkor működik az írás.

---

# Troubleshooting

## Permission denied

### SELinux logok

```bash
sudo ausearch -m AVC
```

---

## Read-only mount

Unmount:

```bash
sudo umount /mnt/share
```

Újramount rw-vel:

```bash
sudo mount -t cifs //192.168.1.10/share /mnt/share \
-o username=sambauser,rw
```

---

## Service ellenőrzés

```bash
systemctl status smb
```

---

## Firewall ellenőrzés

```bash
firewall-cmd --list-all
```

---

# Fontos enterprise kombók

* Samba + SELinux
* Samba + permissions
* Samba + firewall
* Samba + AD integration
