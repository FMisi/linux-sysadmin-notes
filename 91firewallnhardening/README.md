## Tűzfal

| Ubuntu/UFW       | Rocky/firewalld                  |
| ---------------- | -------------------------------- |
| ufw enable       | systemctl enable --now firewalld |
| ufw status       | firewall-cmd --list-all          |
| ufw allow ssh    | firewall-cmd --add-service=ssh   |
| ufw allow 80/tcp | firewall-cmd --add-port=80/tcp   |
| ufw reload       | firewall-cmd --reload            |

Status:

systemctl status firewalld

### Runtime vs permanent

Runtime (ideiglenes):
firewall-cmd --add-service=http

AZONNAL él
reboot/reload után eltűnik

Permanent:

firewall-cmd --add-service=http --permanent

utána KELL:

firewall-cmd --reload

Ez enterprise-ben nagyon fontos különbség.

# Linux (RHEL / Rocky) Hardening

### Nyitott portok

Modern:

```bash
ss -tulpn
```

Régi:

```bash
netstat -ntulp
```

---

### Felesleges service-ek tiltása

```bash
sudo systemctl disable --now cups
sudo systemctl disable --now avahi-daemon
```

---

### Firewalld

Bekapcsolás:

```bash
sudo systemctl enable --now firewalld
```

Állapot:

```bash
firewall-cmd --list-all
```

---

### Firewalld szabályok

SSH:

```bash
sudo firewall-cmd --add-service=ssh --permanent
```

HTTP:

```bash
sudo firewall-cmd --add-service=http --permanent
```

HTTPS:

```bash
sudo firewall-cmd --add-service=https --permanent
```

Samba:

```bash
sudo firewall-cmd --add-service=samba --permanent
```

DNS:

```bash
sudo firewall-cmd --add-service=dns --permanent
```

DHCP:

```bash
sudo firewall-cmd --add-service=dhcp --permanent
```

Reload:

```bash
sudo firewall-cmd --reload
```

---

### NAT / Masquerade

```bash
sudo firewall-cmd --add-masquerade --permanent
sudo firewall-cmd --reload
```

Ellenőrzés:

```bash
sudo firewall-cmd --query-masquerade
```

---

### Hálózati interfészek

```bash
ip addr
```

---

### Routing

Routing tábla:

```bash
ip route
```

---

### SELinux

Állapot:

```bash
getenforce
```

Context:

```bash
ls -Z
```

Audit log:

```bash
sudo ausearch -m AVC
```

---

### SSH hardening

Config:

```bash
sudo nano /etc/ssh/sshd_config
```

Ajánlott:

```conf
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
```

Restart:

```bash
sudo systemctl restart sshd
```

---

### USB tiltása

```bash
echo "blacklist usb-storage" | sudo tee /etc/modprobe.d/usb-storage.conf
sudo dracut -f
```

---

### Lynis audit

Telepítés:

```bash
sudo dnf install epel-release -y
sudo dnf install lynis -y
```

Audit:

```bash
sudo lynis audit system
```

---

### ClamAV

Telepítés:

```bash
sudo dnf install clamav clamav-update -y
```

Teljes scan:

```bash
sudo clamscan -r / --log=/tmp/clamav_report.log
```

---

### Fail2ban

Telepítés:

```bash
sudo dnf install fail2ban -y
```

Indítás:

```bash
sudo systemctl enable --now fail2ban
```

Status:

```bash
sudo fail2ban-client status
```

---

### Fontos troubleshooting

Portok:

```bash
ss -tulpn
```

Firewall:

```bash
firewall-cmd --list-all
```

SELinux:

```bash
sudo ausearch -m AVC
```

Logok:

```bash
journalctl -xe
```

---

### Fontos enterprise témák

* firewalld
* SELinux
* SSH hardening
* fail2ban
* audit
* logging
* least privilege
* service minimization



## Linux (Kliens) Hardening

### USB tiltása

Debian / Ubuntu:

```bash
echo "blacklist usb-storage" | sudo tee /etc/modprobe.d/usb-storage.conf
sudo update-initramfs -u -k $(uname -r)
```

RHEL / Rocky:

```bash
echo "blacklist usb-storage" | sudo tee /etc/modprobe.d/usb-storage.conf
sudo dracut -f
```

---

### Hálózati service-ek

Nyitott portok:

```bash
sudo apt install net-tools
netstat -ntulp
```

Modern alternatíva:

```bash
ss -tulpn
```

Felesleges service-ek tiltása:

```bash
sudo systemctl disable --now cups-browsed
sudo systemctl disable --now avahi-daemon
```

---

### UFW

Bekapcsolás:

```bash
sudo ufw enable
```

Alap szabályok:

```bash
sudo ufw default deny incoming
sudo ufw default deny forwarding
```

Desktop hardening esetén akár:

```bash
sudo ufw default deny outgoing
```

Ha túl szigorú:

```bash
sudo ufw default allow outgoing
```

---

# Hálózati interfészek

Régi:

```bash
ifconfig -a
```

Modern:

```bash
ip addr
```

---

# DNS / HTTP / HTTPS engedélyezése

```bash
sudo ufw allow out on wlx to 1.1.1.1 proto udp port 53 comment 'allow DNS'
sudo ufw allow out on wlx to any proto tcp port 80 comment 'allow HTTP'
sudo ufw allow out on wlx to any proto tcp port 443 comment 'allow HTTPS'
```

---

# UFW logok

```bash
tail -f /var/log/ufw.log
```

---

# UFW manual

```bash
man ufw
```

---

# Frissítés

Debian / Ubuntu:

```bash
sudo apt update
sudo apt dist-upgrade
```

RHEL / Rocky:

```bash
sudo dnf update -y
```

---

# AppArmor

Ubuntu / Debian:

```bash
sudo aa-status
```

```bash
sudo apt install apparmor-profiles apparmor-utils
```

```bash
sudo aa-enforce /etc/apparmor.d/*
```

RHEL / Rocky esetén inkább SELinux használatos.

---

# Firejail

Desktop sandboxing.

Telepítés:

```bash
sudo dpkg -i firejail_*.deb
```

Példa:

```bash
firejail --seccomp --nonewprivs --private-tmp firefox
```

---

# Lynis audit

Telepítés:

```bash
sudo apt install lynis
```

Audit:

```bash
sudo lynis audit system
```

---

# ClamAV

Telepítés:

```bash
sudo apt install clamav
```

Teljes scan:

```bash
sudo clamscan -r / --log=/tmp/clamav_report.log
```

Gyorsabb scan:

```bash
sudo clamscan -ir malware-master
```

---

# OpenSnitch

Desktop monitorozó / outbound firewall rendszer.


## Windows (Kliens) Hardening

# Windows Kliens Hardening

## Windows Update

GUI:

```powershell
Settings -> Windows Update
```

PowerShell:

```powershell
Get-WindowsUpdate
Install-WindowsUpdate
```

---

# Windows Defender

Állapot:

```powershell
Get-MpComputerStatus
```

Gyors scan:

```powershell
Start-MpScan -ScanType QuickScan
```

Teljes scan:

```powershell
Start-MpScan -ScanType FullScan
```

---

# Tűzfal

Firewall bekapcsolása:

```powershell
Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
```

Szabályok listázása:

```powershell
Get-NetFirewallRule
```

Nyitott portok:

```powershell
Get-NetTCPConnection -State Listen
```

---

# Hálózat

IP címek:

```powershell
ipconfig /all
```

Modern:

```powershell
Get-NetIPAddress
```

Routing:

```powershell
route print
```

DNS cache:

```powershell
ipconfig /displaydns
```

DNS flush:

```powershell
ipconfig /flushdns
```

Kapcsolatok:

```powershell
netstat -ano
```

Modern:

```powershell
Get-NetTCPConnection
```

---

# Felesleges service-ek tiltása

Service lista:

```powershell
Get-Service
```

Példa:

```powershell
Stop-Service RemoteRegistry
Set-Service RemoteRegistry -StartupType Disabled
```

---

# RDP hardening

Ajánlott:

* MFA
* VPN mögött
* erős jelszó
* NLA bekapcsolva

RDP státusz:

```powershell
Get-ItemProperty `
'HKLM:\System\CurrentControlSet\Control\Terminal Server'
```

---

# PowerShell Execution Policy

Állapot:

```powershell
Get-ExecutionPolicy
```

Ajánlott:

```powershell
Set-ExecutionPolicy RemoteSigned
```

---

# BitLocker

Állapot:

```powershell
manage-bde -status
```

Bekapcsolás:

```powershell
manage-bde -on C:
```

---

# Lokális userek

Felhasználók:

```powershell
Get-LocalUser
```

Adminok:

```powershell
Get-LocalGroupMember Administrators
```

---

# Jelszó policy

```powershell
net accounts
```

---

# SMB hardening

SMBv1 tiltása:

```powershell
Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol
```

---

# Audit policy

```powershell
auditpol /get /category:*
```

---

# Event Viewer

Megnyitás:

```powershell
eventvwr.msc
```

Fontos logok:

* Security
* System
* Application
* PowerShell

---

# Sysmon

SOC / DFIR szempontból nagyon fontos.

Telepítés:

```powershell
Sysmon64.exe -i
```

---

# Autoruns

Microsoft Sysinternals tool.

Perzisztencia ellenőrzés:

* Run keys
* scheduled tasks
* services
* startup entries

---

# Process-ek

```powershell
tasklist
```

Modern:

```powershell
Get-Process
```

---

# Scheduled taskok

```powershell
Get-ScheduledTask
```

---

# PowerShell history

```powershell
Get-Content `
$env:APPDATA\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt
```

---

# Hardening ajánlások

* MFA
* least privilege
* Defender bekapcsolva
* SMBv1 tiltása
* BitLocker
* RDP limitálása
* PowerShell logging
* rendszeres frissítés

---

# Fontos troubleshooting

Service-ek:

```powershell
Get-Service
```

Logok:

```powershell
eventvwr.msc
```

Kapcsolatok:

```powershell
Get-NetTCPConnection
```

Portok:

```powershell
Get-NetTCPConnection -State Listen
```

Firewall:

```powershell
Get-NetFirewallRule
```

---

