## DNS

sudo dnf install bind -y

sudo nano /etc/named.conf

Main config
sudo nano /etc/named.conf

Keresd ezt:

listen-on port 53 { 127.0.0.1; };

és írd át:

listen-on port 53 { any; };

Meg ezt:

allow-query { localhost; };

erre:

allow-query { any; };
Zone hozzáadás

A file végére:
```bash
zone "lab.local" IN {
    type master;
    file "lab.local.db";
};
```

Zone file
sudo nano /var/named/lab.local.db

Tartalom:
```bash
$TTL 1D
@   IN SOA  ns1.lab.local. root.lab.local. (
        2026052601
        1D
        1H
        1W
        3H )

@       IN NS    ns1.lab.local.
ns1     IN A     192.168.1.10
rocky   IN A     192.168.1.10
debian  IN A     192.168.1.100
```

Jogosultság

sudo chown root:named /var/named/lab.local.db


Config check
sudo named-checkconf
sudo named-checkzone lab.local /var/named/lab.local.db


Firewall
sudo firewall-cmd --add-service=dns --permanent
sudo firewall-cmd --reload


DNS indítás

sudo systemctl enable --now named

Debian kliens

DNS szervernek:

192.168.1.10

TEHÁT DHCP KONFIGBAN:
```
option domain-name-servers 192.168.1.10;
```

vagyis:
"data": "8.8.8.8"

helyett:

"data": "192.168.1.10"

Utána:
sudo systemctl restart kea-dhcp4

Debianon:
sudo dhclient -r
sudo dhclient

Ellenőrzés:

cat /etc/resolv.conf

Ha:

nameserver 192.168.1.10

akk OK.

Teszt

Debianon:

dig rocky.lab.local

vagy:

nslookup rocky.lab.local

Ha visszaadja:

192.168.1.10

akkor működik

