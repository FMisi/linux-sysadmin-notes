# Linux Logging / Logok — RHEL / Rocky Linux jegyzet

# journalctl

Systemd log viewer.

Összes log:

```bash
journalctl
```

Realtime:

```bash
journalctl -f
```

Mai logok:

```bash
journalctl --since today
```

Boot logok:

```bash
journalctl -b
```

Előző boot:

```bash
journalctl -b -1
```

---

# Service logok

Apache:

```bash
journalctl -u httpd
```

Samba:

```bash
journalctl -u smb
```

SSHD:

```bash
journalctl -u sshd
```

Realtime:

```bash
journalctl -u httpd -f
```

---

# Hibák szűrése

Error:

```bash
journalctl -p err
```

Warning:

```bash
journalctl -p warning
```

---

# Klasszikus logok

Log directory:

```bash
ls /var/log
```

Fontos fájlok:

| Log               | Mire jó               |
| ----------------- | --------------------- |
| /var/log/messages | általános rendszerlog |
| /var/log/secure   | auth / SSH            |
| /var/log/cron     | cron                  |
| /var/log/maillog  | mail                  |
| /var/log/httpd/   | Apache                |
| /var/log/samba/   | Samba                 |

---

# tail

Realtime log figyelés:

```bash
tail -f /var/log/messages
```

SSH log:

```bash
tail -f /var/log/secure
```

---

# grep

Keresés:

```bash
grep sshd /var/log/secure
```

Error keresés:

```bash
grep error /var/log/messages
```

---

# dmesg

Kernel logok:

```bash
dmesg
```

USB:

```bash
dmesg | grep usb
```

Disk:

```bash
dmesg | grep sda
```

---

# Auth logok

Sikertelen login:

```bash
grep "Failed password" /var/log/secure
```

SSH loginok:

```bash
grep sshd /var/log/secure
```

---

# SELinux logok

AVC deny:

```bash
sudo ausearch -m AVC
```

Miért blokkolt:

```bash
sudo sealert -a /var/log/audit/audit.log
```

---

# Log rotate

Config:

```bash
ls /etc/logrotate.d
```

Kézi futtatás:

```bash
sudo logrotate -f /etc/logrotate.conf
```

---

# rsyslog

Status:

```bash
systemctl status rsyslog
```

Config:

```bash
/etc/rsyslog.conf
```

---

# tcpdump

Network logging/debug.

DNS:

```bash
sudo tcpdump port 53
```

DHCP:

```bash
sudo tcpdump port 67
```

HTTP:

```bash
sudo tcpdump port 80
```

---

# Apache logok

Access log:

```bash
tail -f /var/log/httpd/access_log
```

Error log:

```bash
tail -f /var/log/httpd/error_log
```

---

# Samba logok

```bash
ls /var/log/samba
```

---

# SSH brute force felismerés

```bash
grep "Failed password" /var/log/secure
```

---

# Disk usage

Logok sok helyet foglalhatnak.

```bash
du -sh /var/log/*
```

---

# Fontos troubleshooting

Service status:

```bash
systemctl status httpd
```

Realtime log:

```bash
journalctl -f
```

Portok:

```bash
ss -tulpn
```

---

# Enterprise szempontból fontos

A logok:

* monitoring
* SIEM
* incident response
* audit
* troubleshooting

alapjai.

Nagyon fontos:

* journald
* rsyslog
* auth logs
* service logs
* SELinux logs

---

# fontos témák

* journalctl
* tail -f
* grep
* auth logs
* rsyslog
* logrotate
* SELinux logs
* troubleshooting

---

# Gyakori hibák

| Hiba               | Ok                    |
| ------------------ | --------------------- |
| service nem indul  | config error          |
| permission denied  | SELinux               |
| nincs log          | service nem fut       |
| disk full          | túl nagy log          |
| connection refused | firewall vagy service |


## nagyon alapok:

```text 
service -> log
```

😄

Linuxon minden:

* service
* kernel
* auth
* network

ír logot.

---

## Nagyon alap parancsok:

Logok:

```bash
ls /var/log
```

Realtime figyelés:

```bash
tail -f /var/log/messages
```

Systemd log:

```bash
journalctl
```

Realtime:

```bash
journalctl -f
```

Service log:

```bash
journalctl -u httpd
```

---

Keresés:

```bash
grep error /var/log/messages
```

---

SSH failed login:

```bash
grep "Failed password" /var/log/secure
```

---

SELinux deny:

```bash
sudo ausearch -m AVC
```

---

