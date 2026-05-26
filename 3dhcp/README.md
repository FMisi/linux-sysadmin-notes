## DHCP

ip a

## statikus IP állítása

sudo nmcli con show
sudo nmcli con mod "1. vezetékes kapcsolat" ipv4.addresses 192.168.1.10/24
sudo nmcli con mod "Wired connection 1" ipv4.method manual
sudo nmcli con mod "Wired connection 1" ipv4.gateway 192.168.1.1
sudo nmcli con up "Wired connection 1"
`A kapcsolat sikeresen aktiválva[...]`

## DHCP beállítás

Rocky <10:
sudo dnf install dhcp-server -y
sudo nano /etc/dhcp/dhcpd.conf

csak ez legyen:
```bash
option domain-name "lab.local";
option domain-name-servers 8.8.8.8;

default-lease-time 600;
max-lease-time 7200;

authoritative;

subnet 192.168.1.0 netmask 255.255.255.0 {
    range 192.168.1.100 192.168.1.200;
    option routers 192.168.1.1;
    option broadcast-address 192.168.1.255;
}
```

sudo nano /etc/sysconfig/dhcpd
```bash
DHCPDARGS=enp0s8    
```
(megnézni ip a -val)

sudo systemctl enable --now dhcpd

systemctl status dhcpd

journalctl -u dhcpd


Rocky >=10:

sudo dnf install kea -y
sudo nano /etc/kea/kea-dhcp4.conf

{
  "Dhcp4": {
    "interfaces-config": {
      "interfaces": [ "enp0s8" ]
    },

    "lease-database": {
      "type": "memfile",
      "persist": true,
      "name": "/var/lib/kea/dhcp4.leases"
    },

    "subnet4": [
      {
        "subnet": "192.168.1.0/24",
        "pools": [
          {
            "pool": "192.168.1.100 - 192.168.1.200"
          }
        ],

        "option-data": [
          {
            "name": "routers",
            "data": "192.168.1.1"
          },
          {
            "name": "domain-name-servers",
            "data": "8.8.8.8"
          }
        ]
      }
    ]
  }
}

systemctl enable --now kea-dhcp4

systemctl status kea-dhcp4

journalctl -u kea-dhcp4


## Debian kliens

sudo apt install isc-dhcp-client -y
sudo dhclient
ip a

