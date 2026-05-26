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

DNF

dnf search
dnf info
dnf history
dnf provides

Firewalld

firewall-cmd --list-all
firewall-cmd --add-service=http
firewall-cmd --reload

Nmcli

nmcli con show
nmcli device status
nmcli con up

Systemd

systemctl status
systemctl restart
systemctl enable
systemctl daemon-reload

Journalctl

journalctl -xe
journalctl -u httpd
journalctl -f

SELinux

getenforce
setenforce
restorecon
ls -Z

utolsó tíz parancs:

history | tail

utolsó parancsot újra:

!!

adott history számú parancsot (pl.: 54) újra:

!54

