| Ubuntu/Debian   | Rocky/RHEL       |
| --------------- | ---------------- |
| apt             | dnf              |
| apt install     | dnf install      |
| apt update      | dnf check-update |
| apt upgrade     | dnf update       |
| ufw             | firewalld        |
| apparmor        | SELinux          |
| netplan         | nmcli            |
| apache2         | httpd            |
| bind9           | bind             |
| isc-dhcp-server | dhcp-server      |

DNF<br><br>

dnf search<br>
dnf info<br>
dnf history<br>
dnf provides<br><br>

Firewalld<br><br>

firewall-cmd --list-all<br>
firewall-cmd --add-service=http<br>
firewall-cmd --reload<br>

Nmcli<br><br>

nmcli con show<br>
nmcli device status<br>
nmcli con up<br>

Systemd<br><br>

systemctl status<br>
systemctl restart<br>
systemctl enable<br>
systemctl daemon-reload<br><br>

Journalctl<br><br>

journalctl -xe<br>
journalctl -u httpd<br>
journalctl -f<br><br>

SELinux<br><br>

getenforce<br>
setenforce<br>
restorecon<br>
ls -Z<br><br>

utolsó tíz parancs:<br><br>

history | tail<br><br>

utolsó parancsot újra:<br><br>

!!<br><br>

adott history számú parancsot (pl.: 54) újra:<br><br>

!54<br>

